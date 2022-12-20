# 代码帮助

> 原文：<https://dev.to/afaqumair/help-with-code-52jl>

我正在尝试创建一个函数，它可以向 Camelcase 返回一个字符串。例如，如果我输入“aDaMsMiTh”，它应该会返回“Adamsmith”。

下面的代码有一些错误，但不能解决。

function crazy caps(string 1){
const lower _ string = string 1 . tolowercase()；
let crazy string =“”；
for(设 I = 0；I<lower _ string . length；i++){
const Crazy = lower _ string[I]；
if(I % 2 = = = 1)Crazy string+= Crazy[I]。toupper case()；
else Crazy string+= Crazy[I]；

}
返回 crazystring
}