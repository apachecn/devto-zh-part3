# 通过单击复选框向控制器发送 Ajax 调用

> 原文：<https://dev.to/shuvocste/sending-an-ajax-call-by-clicking-a-checkbox-to-controller-5cl7>

$(文档)。ready(function(){
$(" input[name = ' field _ name ']")。click(function(){
var checked value = $(" input[name = ' field _ name ']:checked ")。val()；
T3】var post _ URL = " controller _ name/function _ name/"+checked value；
$。ajax({
type: "POST "，
url: post_url，
success:function(d){

}//end success
})；//end AJAX
})；
})；