# ASP.NET 核心中使用 JWT 令牌的动态策略声明(和角色声明)

> 原文：<https://dev.to/coolgoose/dynamic-policy-claims-in-aspnet-core-using-jwt-tokens-and-role-claims-19bg>

嗨，伙计们，作为一名开发人员，我敢打赌每个人都有点“懒”，喜欢优化他们的工作流程。

我最近一次感到懒惰是在我经历我最喜欢的项目时，当时我正致力于实现[基于策略的授权](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-2.2)，并且决定每次我想在数据库中实现一个新的角色声明时添加 **AddPolicy** 是违反直觉的。

我想对 Jerrie Pelser 在这方面所做的初步工作表示衷心的感谢。我只需要在这里和那里修改一些东西来匹配角色声明的 JWT 令牌创建。

我强烈推荐阅读他的文章，以更好地理解这个小“黑客”的内部工作原理。

为了让事情更有条理，我在 Auth 文件夹中添加了这些类。

*   JWT 令牌设置

下面是我如何基于角色声明生成 JWT 令牌声明。

```
 private async Task<string> BuildToken(User user)
{
    var claims = new List<Claim> {
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
        new Claim(JwtRegisteredClaimNames.Sub, user.Id),
    };

    var userRoles = await _users.UserManager.GetRolesAsync(user);

    foreach (var userRole in userRoles)
    {
        claims.Add(new Claim("role", userRole));

        var role = await _users.RoleManager.FindByNameAsync(userRole);

        if (role == null)
        {
            continue;
        }

        var roleClaims = await _users.RoleManager.GetClaimsAsync(role);

        foreach (Claim roleClaim in roleClaims)
        {
            claims.Add(roleClaim);
        }
    }

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(issuer: _config["Jwt:Issuer"],
        audience: _config["Jwt:Issuer"],
        claims: claims,
        expires: DateTime.Now.AddMinutes(30),
        signingCredentials: creds
    );

    return new JwtSecurityTokenHandler().WriteToken(token);
} 
```

Enter fullscreen mode Exit fullscreen mode

在数据库中，角色的 ClaimType 被保存为*范围*

*   HasScopeRequirement

```
using System;
using Microsoft.AspNetCore.Authorization;

namespace Craidd.Auth
{
    public class HasScopeRequirement : IAuthorizationRequirement
    {
        public string Issuer { get; }
        public string Scope { get; }

        public HasScopeRequirement(string scope, string issuer)
        {
            Scope = scope ?? throw new ArgumentNullException(nameof(scope));
            Issuer = issuer ?? throw new ArgumentNullException(nameof(issuer));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   授权策略提供者

这是自动策略重解器完成工作的地方。它检查是否已经添加了具有该名称的任何策略，然后添加需求。

```
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Options;

namespace Craidd.Auth
{
    public class AuthorizationPolicyProvider : DefaultAuthorizationPolicyProvider
    {
        private readonly AuthorizationOptions _options;
        private readonly IConfiguration _config;

        public AuthorizationPolicyProvider(IOptions<AuthorizationOptions> options, IConfiguration config) : base(options)
        {
            _options = options.Value;
            _config = config;
        }

        public override async Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
        {
            // Check static policies first
            var policy = await base.GetPolicyAsync(policyName);

            if (policy == null)
            {
                policy = new AuthorizationPolicyBuilder()
                    .AddRequirements(new HasScopeRequirement(policyName, _config["Jwt:Issuer"]))
                    .Build();

                // Add policy to the AuthorizationOptions, so we don't have to re-create it each time
                _options.AddPolicy(policyName, policy);
            }

            return policy;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   哈斯科普汉德勒

这是我们处理映射的地方。我们的 JWT 令牌已经将作用域生成为一个数组，所以我们只需要做基本的检查。

这里要记住的最重要的事情是，你可以将你的 **dbContext** 注入到 *HasScopeHandler* 中，并进行更高级的检查。

```
 using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;

namespace Craidd.Auth
{
    public class HasScopeHandler : AuthorizationHandler<HasScopeRequirement>
    {
        protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, HasScopeRequirement requirement)
        {
            // If user does not have the scope claim, get out of here
            if (!context.User.HasClaim(c => c.Type == "scope" && c.Issuer == requirement.Issuer)) {
                return Task.CompletedTask;
            }

            var scopes = context.User.FindAll(c => c.Type == "scope" && c.Issuer == requirement.Issuer).ToList();

            // // Succeed if the scope array contains the required scope
            if (scopes.Any(s => s.Value == requirement.Scope)) {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   将这些类添加到 Startup.cs 中

```
services.AddAuthorization();
// register the scope authorization handler
services.AddSingleton<IAuthorizationPolicyProvider, AuthorizationPolicyProvider>();
services.AddScoped<IAuthorizationHandler, HasScopeHandler>(); // AddScoped allows you to inject the dbContext 
```

Enter fullscreen mode Exit fullscreen mode