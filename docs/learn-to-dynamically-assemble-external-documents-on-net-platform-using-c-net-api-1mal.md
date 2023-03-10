# 学习在。NET 平台使用 C#。NET API

> 原文：<https://dev.to/groupdocs/learn-to-dynamically-assemble-external-documents-on-net-platform-using-c-net-api-1mal>

过程自动化是一个上升的趋势，我们在未来几年将会非常依赖它。它包含了一个广泛而深远的概念，而不仅仅是让事物变得不那么“依赖人类”的想法。它指的是生产率的提高、时间效率的处理、更好的管理、更少的错误、可靠的输出等等。

基于行业或特定的业务场景，流程自动化可以采取多种形式。以家庭自动化为例如何？难道你不想在你的蛋糕日听到一句美好的生日祝福吗？用语音指令开空调怎么样？或者，根据一天中的时间自动上下移动百叶窗？这些都是家庭自动化服务提供商在全球营销的一些基本福利。

在文档自动化方面没有太大的不同；您总是希望有一个更高效、更可靠、不易出错的文档生成过程，让您在创建法律、金融、合同和医疗以及无数其他类型的文档时更加轻松。

如果您是一名软件开发人员，并且希望以编程方式将文档自动化功能集成到您在。NET 平台上，没有多少 API 可以为您提供与成熟的功能集和无缝处理捆绑在一起的使用便利。在为数不多的可以信任的 API 中，GroupDocs。的装配。NET 肯定是一个要考虑的。

它包含了一系列非常有用的函数，在从不同的数据源(如数据库、JSON、XML、OData 和 Custom)组装各种类型的常用文档格式时，为您提供了优势。NET 对象。这个自动化 API 的一个重要特性是动态组装外部文档的能力。该功能目前适用于 Microsoft Word 和电子邮件格式。

您可以使用文档标记，这些标记是模板中的占位符，允许您在运行时吸收文档。使用这些 doc 标签，程序员可以用。NET API。了解更多—[http://bit.ly/automate-external-documents](http://bit.ly/automate-external-documents)

以下代码片段显示了如何以文字处理格式插入外部文档:

`//Setting up source open document template
const String strDocumentTemplate = "Word Templates/Nested External Document.docx";
//Setting up destination open document report
const String strDocumentReport = "Word Reports/Nested External Document.docx";
try
{
//Instantiate DocumentAssembler class
DocumentAssembler assembler = new DocumentAssembler();
//Call AssembleDocument to generate Report in open document format
assembler.AssembleDocument(CommonUtilities.GetSourceDocument(strDocumentTemplate),
CommonUtilities.SetDestinationDocument(strDocumentReport),
new DataSourceInfo(DataLayer.GetCustomerData(), "customer"));
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}`

同样，以下是如何以电子邮件格式插入外部文档:

`//Setting up source open document template
const String strDocumentTemplate = "Email Templates/Nested External Document.msg";
//Setting up destination open document report
const String strDocumentReport = "Email Reports/Nested External Document.msg";
try
{
//Instantiate DocumentAssembler class
DocumentAssembler assembler = new DocumentAssembler();
//Call AssembleDocument to generate Report in open document format
assembler.AssembleDocument(CommonUtilities.GetSourceDocument(strDocumentTemplate),
CommonUtilities.SetDestinationDocument(strDocumentReport),
new DataSourceInfo(DataLayer.GetCustomerData(), "customer"));
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}`

要下载执行上述代码片段的相关资源，请参见-[http://bit.ly/2ShtYTy](http://bit.ly/2ShtYTy)