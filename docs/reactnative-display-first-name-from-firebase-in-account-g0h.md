# 反应:显示客户的名字

> 原文：<https://dev.to/masterolu21/reactnative-display-first-name-from-firebase-in-account-g0h>

使用 Redux、firebase 和 react native 当用户成功登录时，我想从 firebase 中检索并显示用户的名字。我想显示用户的名字，而不是在我的 Account.js 文件中硬编码一个临时名字。我不知道这样做的正确语法，我不确定这是否需要 Redux，但如果需要，我肯定需要帮助哈哈。

如果我能看到这是如何做到的，我就能知道如何获取和显示其他用户信息，如地址、库存等。

' SignInScreen.js '

燃烧基地
。auth()
。signindretrievedatawithcredential(凭证)
。然后((结果)= > {
console.log('用户已登录')；
if(result . additional userinfo . isnewuser)
{
firebase
。数据库()
。ref( `/users/${result.user.uid}` )
。set({
Gmail:result . user . email，
profile _ picture:result . additional userinfo . profile . picture，
locale:result . additional userinfo . profile . locale，
first _ name:result . additional userinfo . profile . given _ name，
last _ name:result . additional userinfo . profile . family _ name，
created _ at:date . now()
})

' Account.js '

render() {
const {
导航:{ navigate }
} = this . props；
return(

)style = {[common styles . alingselfcenter，{
...ifIphoneX(
{marginTop: 50}，
{ margin top:30 })
}]}
>
style = {[
styles。TextColor，
commonStyles.font18，
common styles . text bold
}
>
我的帐户

/>
T22】style = {[
common styles . text white，
commonStyles.font16，
common styles . text bold
}
>
huss nain _ sarwar(临时名称)