# TypeError:无法读取 undefined-Hubspot 的属性“89”

> 原文：<https://dev.to/rajivsr23/typeerror-cannot-read-property-89-of-undefined-hubspot-16j2>

我试图从 Hubspot 获得所有交易，但出于某种原因，我得到了一些交易的错误。
该错误属于无法读取未定义的属性“89”的类型。

var fetch = require(" node-fetch ")；
getHubspotData([https://api.hubapi.com/deals/v1/deal/paged?hapikey=demo&properties = deal name&properties = deal stage&properties = closedate&properties = deal type&properties = type&properties = hubspot _ owner _ id&properties = amount&properties = notes _ last _ updated&include associations = true’](https://api.hubapi.com/deals/v1/deal/paged?hapikey=demo&properties=dealname&properties=dealstage&properties=closedate&properties=dealtype&properties=type&properties=hubspot_owner_id&properties=amount&properties=notes_last_updated&includeAssociations=true'))；
异步函数 getHubspotData(URL){
console . log(" URL:"+URL)；
var 迭代次数= 0；
for(设 I = 0；我<100；i++){
wait fetch(URL)
。然后((resp) = > resp.json()) //将数据转换成 json
。catch((错误)= > {
console.log("错误处理 JSON: " +错误)
})。Then(function(data){
console . log(" Then-3 ")；
迭代次数=迭代次数+1；
console.log("I: " +迭代)；
if((data . has more = = false)&&(I = = data . Deals . length)){
console . log("从 Hubspot 同步所有交易")；
console.log("程序终止..............................");
process . exit()；
}
if(data = = = undefined){
console . log(" data undefined/////////////////////////////////////////////////；
获取(网址)
。然后((resp) = > resp.json())
。然后(function(data){
console . log(data . deals . length)；
}
)
} else {
console . log("数据未定义")；
}
var dealId = data . deals[I]。dealId
var deal name = data . deals[I]. properties . deal name . value；
console . log(deal name)；
offset = data . offset；
has more = data . has more；
deal length = data . deals . length；
if((has more = = true)&&(I+1>= deal length)){
console . log(" NEW LOOP>>>>>>>>>>>>>>>>>>>>>
var URL = '[https://api.hubapi.com/deals/v1/deal/paged?hapikey=demo&properties = deal name&properties = deal stage&properties = closedate&properties = deal type&properties = type&properties = hubspot _ owner _ id&properties = amount&properties = notes _ last _ updated&include associations = true&offset =](https://api.hubapi.com/deals/v1/deal/paged?hapikey=demo&properties=dealname&properties=dealstage&properties=closedate&properties=dealtype&properties=type&properties=hubspot_owner_id&properties=amount&properties=notes_last_updated&includeAssociations=true&offset=)'+offset；
getHubspotData(网址)；
}
})。catch((Error)=>{
console . log(" Error get the deals////////////////////////////////////////////////+Error)
})
}

而且看起来是随机的。在一次执行中，它可能会对交易 69 抛出一个错误——无法读取未定义的属性“69 ”,下次我运行它时，它会对其他一些交易抛出错误。我试图用一个 if 条件来检查数据是否“未定义”,但这没有用。我不知道为什么会出现这个错误，也找不到解决这个问题的方法。非常感谢对此事的任何帮助！