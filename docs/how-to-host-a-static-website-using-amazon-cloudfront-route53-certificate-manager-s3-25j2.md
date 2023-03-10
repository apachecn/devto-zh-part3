# 如何使用亚马逊 CloudFront 托管静态网站，Route53，证书管理器，S3

> 原文：<https://dev.to/sanamsoodan/how-to-host-a-static-website-using-amazon-cloudfront-route53-certificate-manager-s3-25j2>

使用 AWS 建立自己的网站时，我意识到找到所有正确的信息是多么令人困惑。所以我决定站出来自己写博客。

我的目标是让你通过一些步骤，这样你就可以在几分钟内建立并运行你的网站。这里有一些成功托管网站的步骤和清单。

第一步:注册域名
购买你选择的域名。你可以从 AWS、Google domains、Go Daddy 和其他提供商那里购买你的域名。对于这个博客的范围，我们将认为域是 sanamdeep.dev。您将使用您选择的域，并用下面步骤中的用法替换它们。
要从 AWS 购买域名，请访问 Route53 并查看您选择的可用域名。
第二步:AWS 设置
这一步包括多个子步骤。

Setting up ACM or AWS Certificate Manager.
ACM handles the complexity of creating and managing public SSL/TLS certificates for your AWS based websites and applications.
Go to AWS Certificate Manager and select “Request a certificate” > “Request a public certificate” > Under domain name enter sanamdeep.dev and *.sanamdeep.dev . The second domain name is for all the subdomains like [www.sanamdeep.dev](http://www.sanamdeep.dev) . > choose “DNS validation” > Review > confirm and request.
After this step if you have bought your domain from AWS then you will have to wait for 10–15 minutes while the certificate is issued. Otherwise if you have bought your domain from a third party domain provider then you will need to click on “ Export DNS configuration to a file” option inside your requested certificate and copy the DNS Name and Values to your domain settings.
Eg for Google Domain you can go to your particular domain and under DNS > Custom resource records you will add the DNS entries. Only enter unique entries.
Only after this step is completed will AWS take 10–15 minutes to issue a certificate.
Set up S3 Buckets
Go to S3 and make 2 buckets [www.sanamdeep.dev](http://www.sanamdeep.dev) and sanamdeep.dev .
We are creating two buckets so a request from sanamdeep.dev will be redirected to [www.sanamdeep.dev](http://www.sanamdeep.dev)
Go to bucket sanamdeep.dev and under Permissions > Access public settings edit all the below properties to be false.
Next under Properties > Static website hosting > choose Redirect requests and set target as [www.sanamdeep.dev](http://www.sanamdeep.dev) and protocol as https.
Now Go to bucket [www.sanamdeep.dev](http://www.sanamdeep.dev) and under Permissions > Access public settings edit all the below properties to be false.
and put the following policy under Bucket Policy
{
“Version”: “2012–10–17”,
“Statement”: [
{
“Sid”: “PublicReadForGetBucketObjects”,
“Effect”: “Allow”,
“Principal”: “*”,
“Action”: “s3:GetObject”,
“Resource”: “arn:aws:s3:::[www.sanamdeep.dev/*”](http://www.sanamdeep.dev/*%E2%80%9D)
}
]
}
Next under Properties > Static website hosting > Use this bucket to host a website and under index document put index.html. You can now test the endpoint. For the purpose of a sample website use a Hello world HTML file.
While adding files to [www.sanamdeep.dev](http://www.sanamdeep.dev) make sure to change Manage public permissions to Grant public read access to this object(s).
Setting up CloudFront
Now we will set up two CloudFront one for sanamdeep.dev and the other for [www.sanamdeep.dev](http://www.sanamdeep.dev)
First lets create CloudFront from [www.sanamdeep.dev](http://www.sanamdeep.dev).
Create Distribution > Web > Get Started > Under Origin domain path you need to copy the endpoint for S3 bucket for [www.sanamdeep.dev](http://www.sanamdeep.dev) . You can go to the S3 bucket and under properties > Static website hosting you can find the endpoint.
Note: do not select the option displayed under Origin domain path.
Upon entering the Origin domain path the Origin ID is automatically populated.
Under Default Cache Behavior Settings > Viewer Protocol Policy choose Redirect Http to Https.
Under Distribution Settings > Alternate Domain Names(CNAMEs) > enter [www.sanamdeep.dev](http://www.sanamdeep.dev)
Under Distribution Settings > SSL Certificate >choose Custom SSL certificate and choose your certificate
Under Distribution Settings > Default Root Object type index.html
Click Create Distribution. After this CloudFront will take 20–30 minutes to be deployed.
Repeat the process for sanamdeep.dev
Get the endpoint from sanamdeep.dev bucket.
Use sanamdeep.dev as the Alternate Domain Names(CNAMEs)
No need to enter index.html under Distribution Settings > Default Root Object for sanamdeep.dev
Rest of the settings will be similar to the previous CloudFront.
Setting up Route53
In this step Under Hosted Zones > Create Hosted Zones
Enter Domain as sanamdeep.dev and click create.
Now under the newly created hosted zone sanamdeep.dev you will find two automatically created record sets.
One of them will have the Name servers with four entries.
Now Click Create Record Sets >under Name put sanamdeep.dev >
check Alias as yes > choose alias target as the corresponding CloudFront. Make sure to choose the right alias Target.
Create a Record Set for [www.sanamdeep.dev](http://www.sanamdeep.dev) following the above steps.
Copying the DNS entries
For Third party providers we already copied the DNS entries from certificate manager. But for people using domain from AWS you will need to copy the DNS entries under Route53 if not added automatically.
Copying Name Servers
This step is for domains from third-party providers copy the Name Servers generated in Route53 and add them to the domain. Eg for google domains they can be added under DNS> Name Servers
After this step, you can open your website either by [www.sanamdeep.dev](http://www.sanamdeep.dev) or sanamdeep.dev. Both will be redirected to [https://www.sanamdeep.dev](https://www.sanamdeep.dev)

网站上没有反映的变化
同样，如果你通过改变 S3 桶中的内容来改变你的网站。CloudFront 可能需要一段时间才能自动反映这些变化。一个更快的方法是去 CloudFront>Invalidations>Create Invalidation，如果你想使所有文件无效，输入/*。在 [www.sanamdeep.dev](http://www.sanamdeep.dev) 和 sanamdeep.dev 上创建无效后，更改将立即反映出来。