# 使用 C#比较文件和图表，检测 PowerPoint 幻灯片中的样式。NET API

> 原文：<https://dev.to/groupdocs/compare-files-and-charts-detect-styles-in-powerpoint-slides-using-c-net-api-5gc6>

每月新发布的文档比较。NET API 包括一些关键的增强和一个重要的错误修复。在最新版本中，应用程序开发人员可以在中使用 Microsoft PowerPoint 演示文稿时比较图表并检测样式。网络应用。幻灯片文件中与使用自动形状相关的错误已被修复，从而提供了更好的使用体验。Microsoft Word 文档中的 Comparison tester 功能改进是 group docs 18.11 版本的另一个亮点。的比较。NET API-[http://bit.ly/2SRtb8s](http://bit.ly/2SRtb8s)

下面的代码示例演示了如何比较两个文档并将它们保存到一个文件中:

```
 public static void CompareDcumentsFromStreamToOutputFile()
    {
        // Create two streams of documents
        Stream sourceStream = File.Open(Path.Combine(Common.sourcePath, Common.sourceFile), FileMode.Open, FileAccess.Read);
        Stream targetStream = File.Open(Path.Combine(Common.targetPath, Common.targetFile), FileMode.Open, FileAccess.Read);

        // Get instance of GroupDocs.Comparison.Comparer and call method Compare.
        GroupDocs.Comparison.Comparer comparison = Common.getComparison();
        ICompareResult result = comparison.Compare(sourceStream, targetStream, new ComparisonSettings { DeletedItemsStyle = new StyleSettings { StrikeThrough = true }, GenerateSummaryPage = true, DetailLevel = DetailLevel.Hight });

        // save result document to a file.
        result.SaveDocument(Path.Combine(Common.resultPath, Common.resultFile));

        sourceStream.Close();
        targetStream.Close();
    } 
```

GroupDocs API 的 YouTube 视频教程—[http://bit.ly/2RZOfcG](http://bit.ly/2RZOfcG)