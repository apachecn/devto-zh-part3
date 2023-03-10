# 在 C#中合并 PDF 文件

> 原文：<https://dev.to/alijagoseif/merge-pdf-files-in-c-2k74>

在本文中，我将向您展示如何使用 Merge_File 方法和 [easy PDF SDK](http://pdfonline.com/pdf-sdk/) 以编程方式合并多个 PDF 文件。这个 C#示例程序演示了如何使用 PDFProcessor API 合并 PDF 页面。

文件名假定在数组文件[]中。合并的文件保存在“outFileName”下

```
private void Merge_File(string[] files, string outFileName)
{
    try
    {
        PDFProcessor oPDFProcessor = new PDFProcessor();
            // loop over all the files, which are concatenated one by one
            string inFileName = files[0];

        for(int i = 1; i < files.Length; i++)
        {
            oPDFProcessor.Merge(inFileName, files[i], outFileName);         
            inFileName = outFileName;
        }               

        MessageBox.Show(files.Length.ToString() + " files merged!");
    }
    catch(System.Runtime.InteropServices.COMException err)
    {
        MessageBox.Show(err.Message + " (" + err.ErrorCode.ToString() + ")");
    }
}

```