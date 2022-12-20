# 在 C#中旋转 PDF 页面

> 原文：<https://dev.to/alijagoseif/rotating-pdf-page-in-c-6ol>

这里我将向你展示如何用 RotatePage 方法在 C#中旋转 PDF。我使用 BCL 的 [PDF 库](https://www.pdfonline.com/c%23-pdf-library/)处理 PDF 文件中的数据。它还允许您准确地创建、操作和转换来自多个来源的文件为 PDF。

允许处理和操作现有 PDF 文档的 PDF 处理器 API 有几种方法。进行页面旋转的那个叫做“RotatePages”。

下面的代码会旋转页面。所有的参数都应该在这个字符串中设置:
`oPDFProcessor.RotatePages(string InputFileName,string OutputFileName,int From,int To,prcPageRotation.PRC_ROT_{angle}_DEG
MessageBox.Show("Rotate Success!");`

**参数:**
InputFileName -输入文件名；
OutputFileName -输出文件名；
From -旋转页面 From(第一页= 0)；
将页面旋转到；
角度-0 | 90 | 180 | 270 |顺时针|逆时针|逆时针|倒置。

这段代码演示了如何旋转页面。该代码将 PDF 文件中的第一页旋转 90 度。

```
private void Rotate_Test(string inFile, string outFile)
{
    try
    {
        PDFProcessor oPDFProcessor = new PDFProcessor();
            // rotate the first page 90 by degrees
oPDFProcessor.RotatePages(inFile,outFile,0,0,prcPageRotation.PRC_ROT_90_DEG 
        MessageBox.Show("Rotate Success!");
    }
    catch(System.Runtime.InteropServices.COMException err)
    {
        MessageBox.Show(err.Message + " (" + err.ErrorCode.ToString() + ")");
    }
}

```