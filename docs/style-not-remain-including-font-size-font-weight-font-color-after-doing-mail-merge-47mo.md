# 不保留样式(包括字体大小、字体粗细、字体颜色，...)进行邮件合并后

> 原文：<https://dev.to/gialoui/style-not-remain-including-font-size-font-weight-font-color-after-doing-mail-merge-47mo>

我和阿斯彭一起工作，面对这些问题:

合并后应用于合并字段的样式全部丢失:
[![Alt text of image](img/03f01cbc59165e5bdbf45549d9d4ff09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_sMSvkLC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9t4grkk2mbq5la9ijr1.png)

然后，我尝试了另一种方法，删除了字段代码中的 MERGEFIELD 关键字，正确地保留了应用的样式，但不太真实。合并已被删除的字段这些关键字堆积在文档顶部(请忽略版权警告):
[![Alt text of image](img/a98ef95d9c678b4c890ddde4715cb3de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6OL6A8pm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/33i9tjy8op7dljyjua5j.png)

这是我执行邮件合并过程的代码:

```
public static void generateMailMergePdfFromJson(Document template, String json, String outputFilePath) throws ParserConfigurationException, SAXException, IOException, Exception {
    //Remove merge field with empty value and if the row are empty=> remove paragraph
    template.getMailMerge().setCleanupOptions(MailMergeCleanupOptions.REMOVE_UNUSED_FIELDS
            | MailMergeCleanupOptions.REMOVE_EMPTY_PARAGRAPHS
            | MailMergeCleanupOptions.REMOVE_UNUSED_REGIONS
            | MailMergeCleanupOptions.REMOVE_CONTAINING_FIELDS);

    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(json);

    ArrayNode arrayNode = objectMapper.createArrayNode();
    arrayNode.add(jsonNode);

    JsonNode rootNode = objectMapper.createObjectNode();

    ((ObjectNode) rootNode).set(Dictionary.ROOT_TAG_NAME, arrayNode);

    //To call custom merge data with html code
    template.getMailMerge().setFieldMergingCallback(new HandleMergeFieldInsertHtml());

    // Add main merge field based of main root tag name
    template.accept(new TableTagHandler(Dictionary.ROOT_TAG_NAME));
    // Merge fields
    template.getMailMerge().executeWithRegions(new JsonMailMergeDataSet(new JsonDocument(rootNode, Dictionary.ROOT_TAG_NAME)));

    template.save(outputFilePath, SaveFormat.PDF);
} 
```

任何和阿斯彭一起工作过的人，请给我一个关于这个的建议。