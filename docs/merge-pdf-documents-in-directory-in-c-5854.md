# 在 C#中合并目录中的 PDF 文档

> 原文：<https://dev.to/alijagoseif/merge-pdf-documents-in-directory-in-c-5854>

在我的第一篇文章中，我将为您提供一种方法来合并特定目录中的所有 pdf 文件。完整的方法显示在下面的 [PDF C#](https://www.pdfonline.com/easypdf/sdk/programming-pdf/csharp/) 代码中。请随意将其完整复制并粘贴到您的应用程序中。我们将一起讨论在 C#中合并 PDF 文件的所有步骤。

首先，请记住向您的项目添加适当的引用，并使用条目来伴随这些引用。您的项目需要添加 [C# PDF 库](https://www.pdfonline.com/c%23-pdf-library/)处理器引用，并且需要以下“使用”条目:

使用系统；
使用系统。木卫一；
使用系统。收藏；
使用 bcl . easy pdf 8 . interop . easy pdf processor；

方法的名称如下:
 `public static string MergeAllPDFFilesInDIR(string inFileDir, string OutFileName)
{`

建议取两个字符串变量，返回一个字符串变量。返回值是一个字符串，它将包含状态报告，或者是成功转换的报告，带有生成的输出文件的名称，或者是最佳的错误报告。inFileDir 是确定输入目录的地方，而 OutFileName 是输出文件名。

inFileDir 必须使用这种格式:@"C:\YourFolderName\ "。另一方面 OutFileName 需要遵循这样的格式:@ " C:\ your foldername \ outname . pdf "。
否则，您可以为 OutFileName 输入一个 null 值，该方法将自动生成一个具有自己名称的输出文件名。

这就是我们声明主要变量和对象的方式。
 `//Declare Major Variables and Objects
string result = null;
DirectoryInfo Dir = null;
FileInfo[] FileList = null;
ArrayList InputFileList = null;
PDFProcessor oMerger = null;` 

一旦声明了变量和对象，您需要通过一个小的 if-then 语句来检查输入目录是否存在。它还检查是否有一个空变量作为 OutputFileName 传递，如果有，就在输入目录中生成一个文件名。

 `// Identify inFileDir and OutFileName
Dir = new DirectoryInfo(inFileDir);
if (!Dir.Exists)
{
result = "Directory : " + inFileDir + " : Does not Exist";
return result;
}
if (OutFileName == null)

//If OutFileName is NUll, provide a
//Default File Name and Location
{
OutFileName = inFileDir + "_1_Merged.pdf";
}` 

之后，我们需要将所有的输入文件收集到 ArrayList 对象中，如上所示。如果目录中没有 PDF 文件，将返回一个错误，程序将结束。否则，目录中的所有 PDF 文件都将被一个接一个地加载到 ArrayList 对象中，以便进一步合并。

`// Gather Input Files
FileList = Dir.GetFiles("*.pdf", SearchOption.AllDirectories);
InputFileList = new ArrayList();
foreach (FileInfo FI in FileList) { InputFileList.Add(FI.FullName);`

`if (InputFileList.Count == 0)
{
result = "There were no PDF Files in : " + inFileDir;
return result;
}`

当输入文件被收集时，该方法创建 Processory 对象并初始化它。此外，它将许可证密钥(使用 C# PDF 库时需要)应用于对象。你可以在 [PDF SDK](https://www.pdfonline.com/pdf-sdk/) 网站上获得免费的。

`// Create Processor Object
try
{
oMerger = new PDFProcessor();
oMerger.LicenseKey = "475D-E1ED-5336-DD76-2C5E-FDAE";
//If you are using the BCL Licensing System, enter your License Key Here;
}
catch (Exception errCreate)
{
result = "Attempt to create PDFProcessor oMerger Failed : Message : "
+ errCreate.Message + " : Inner Exception : "
+ errCreate.InnerException;
return result;
}`

一旦对象被初始化，实际的合并在下面的代码中执行。

`//Run MergeBatch
try
{
oMerger.MergeBatch(InputFileList.ToArray(), OutFileName);
}
catch (Exception errRun)
{
result = "Attempt to Merge the Documents Failed : Message : "
+ errRun.Message + " : Inner Exception : " + errRun.InnerException;
return result;
}`

`result = "Merging Complete : Output File located at : " + OutFileName;
return result;
}`

如果一切正常，您会收到一条消息，表明合并已经完成。输出文件位于您指定的位置。因为我们将 OutFileName 字符串格式化为包含整个文件地址，所以会告诉您文件的名称和位置。如果有任何错误，应该报告出来。

下面是完整的方法代码，随时可以添加到任何 C#应用程序中:

```
public static string MergeAllPDFFilesInDIR(string inFileDir, string OutFileName)
    {
            //Declare Major Variables and Objects
        string result = null;
        DirectoryInfo Dir = null;
        FileInfo[] FileList = null;
        ArrayList InputFileList = null;
        PDFProcessor oMerger = null;

            // Identify inFileDir and OutFileName
        Dir = new DirectoryInfo(inFileDir);
        if (!Dir.Exists)
        {
            result = "Directory : " + inFileDir + " : Does not Exist";
            return result;
        }
        if (OutFileName == null)    
        //If OutFileName is NUll, provide a
        //Default File Name and Location
        {
            OutFileName = inFileDir + "_1_Merged.pdf";
        }

            // Gather Input Files
        FileList = Dir.GetFiles("*.pdf", SearchOption.AllDirectories);
        InputFileList = new ArrayList();
        foreach (FileInfo FI in FileList) { InputFileList.Add(FI.FullName); }

        if (InputFileList.Count == 0)
        {
            result = "There were no PDF Files in : " + inFileDir;
            return result;
        }

            // Create Processor Object
        try
        {
            oMerger = new PDFProcessor();
            oMerger.LicenseKey = "475D-E1ED-5336-DD76-2C5E-FDAE";    
            //If you are using the BCL Licensing System, enter your License Key Here;
        }
        catch (Exception errCreate)
        {
            result = "Attempt to create PDFProcessor oMerger Failed : Message : " 
                + errCreate.Message + " : Inner Exception : "
                + errCreate.InnerException;
            return result;
        }

            //Run MergeBatch
        try
        {
            oMerger.MergeBatch(InputFileList.ToArray(), OutFileName);
        }
        catch (Exception errRun)
        {
            result = "Attempt to Merge the Documents Failed : Message : " 
                + errRun.Message + " : Inner Exception : " + errRun.InnerException;
            return result;
        }

        result = "Merging Complete : Output File located at : " + OutFileName;
        return result;
    }

```

如果你对这个方法有任何疑问或问题，或者你有任何你想让我创造的方法的想法，请随时联系我。

好好编码！