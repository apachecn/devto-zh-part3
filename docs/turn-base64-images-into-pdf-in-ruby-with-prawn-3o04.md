# 将 Base64 图像转换为 PDF 格式，并在红宝石中显示大虾

> 原文：<https://dev.to/gadimbaylisahil/turn-base64-images-into-pdf-in-ruby-with-prawn-3o04>

几周前，我正在开发 [Camaloon](https://www.camaloon.com) 中的一个功能，但我最终没有使用它。不过，我觉得分享一下还是挺好玩的。

所以，让我们直接进入正题，不要重复样板文件，因为现在已经是深夜了，我还得早起:)。

> 问题:
> 我试图实现一个第三方 API，并希望得到一个 Base64 编码的 PDF 文件，其中包含我试图获取的不同文档。不幸的是，第三方 API 没有提供 PDF 文件，而是提供了图片。

所以，我想好吧，我就用[大虾](https://github.com/prawnpdf/prawn)把所有的图片塞进大虾的 pdf 文档，然后就完成了。但是，图片是 GIF 格式的，我必须把它们转换成对虾支持的格式，如 PNG/JPEG。

于是，看到这些问题，`Base64ImageToPdfConverter`诞生了。我想保持类的通用性，以防将来我们需要重用它。

> 解决方案:接受一组 Base64 编码的图像，检查它们是否需要转换成 PNG(对虾支持的格式)。将它们转换成 PNG，以防它们需要转换并包含在对虾文档中，如果不需要，则直接包含它们。

让我们创建我们的类。

```
require 'prawn'
require 'tempfile'
require 'base64'

class Base64ImageToPdfConverter
  PRAWN_SUPPORTED_MIME_TYPES= ['image/png', 'image/jpeg'].freeze

  def initialize(base64_images:)
    @base64_images = base64_images
  end

  def run!
    return if base64_images.empty?

    open_tempfile(['combined-pdf', '.pdf']) do |pdf_file|
      base64_images.each do |base64_image|
        with_image_path(base64_image) do |image_file|
          # Process and Insert image to Prawn document
        end
      end
      # Render content to Prawn Document
      # Encode PDF in base64
    end
  end

  private
  attr_reader :base64_images

  def open_tempfile(*args)
    f = Tempfile.new(*args)
     yield(f)
    ensure
     f.close!
  end

  def with_image_path(base64_image)
    open_tempfile('image') do |f|
      f.write Base64.decode64(base64_image)
      f.flush
      yield f
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

那到底是什么？这里，我们为最终的 PDF 创建一个临时文件，我们将合并所有图像，并为每个图像创建一个临时文件，解码 Base64 并写入其中，因为我们很快就会需要它来处理 ImageMagick 转换。

让我们添加我们的转换逻辑。

```
require 'prawn'
require 'tempfile'
require 'base64'
class Base64ImageToPdfConverter
  PRAWN_SUPPORTED_MIME_TYPES= ['image/png', 'image/jpeg'].freeze

  def initialize(base64_images:)
    @base64_images = base64_images
  end

  def base64_pdf
    return if base64_images.empty?

    open_tempfile(['combined-pdf', '.pdf']) do |pdf_file|
      base64_images.each do |base64_image|
        with_image_path(base64_image) do |image_file|
          insert_image(image(image_file.path))
        end
      end
      # render and return the Base64 encoded PDF.
      prawn_document.render_file pdf_file.path
      base64_encoded(pdf_file.path)
    end
  end

  private

  attr_reader :base64_images
  attr_accessor :prawn_document

  def insert_image(string_io)
    prawn_document.start_new_page
    prawn_document.image string_io, position: :center
  end

  def image(file_path)
    if need_conversion?(file_path)
      # We are using imagemagick convert command to convert to png
      system "convert #{file_path}  #{file_path}.png"

      # As we do not need a Tempfile but an object that responds to
      # read and write to use in Prawn document. We can use StringIO and
      # unlink tempfile.

      string_io= StringIO.new(File.read("#{file_path}.png"))
      File.unlink("#{file_path}.png")
    else
      string_io= StringIO.new(File.read(file_path))
    end

    string_io
  end

  def need_conversion?(file_path)
    !PRAWN_SUPPORTED_MIME_TYPES.include?(mime_type(file_path))
  end

  def mime_type(file_path)
    `file #{file_path} --mime-type -b`.strip
  end

  def prawn_document
    @prawn_document ||= Prawn::Document.new margin: 0,
                                            skip_page_creation: true
  end

  def base64_encoded(file_path)
    Base64.encode64(File.read(file_path))
  end

  def open_tempfile(*args)
    f = Tempfile.new(*args)
    yield(f)
  ensure
    f.close!
  end

  def with_image_path(base64_image)
    open_tempfile('image') do |f|
      f.write Base64.decode64(base64_image)
      f.flush
      yield f
    end
  end
end

base64_gifs = ['R0lGODlhAQABAIAAAAUEBAAAACwAAAAAAQABAAACAkQBADs=']

converter = Base64ImageToPdfConverter.new(base64_images: base64_gifs)
puts converter.base64_pdf 
```

Enter fullscreen mode Exit fullscreen mode

所以我们开始了，我们检查每个图像的 Mime 类型，使用 ImageMagick 的`convert`命令将它们转换为`PNG`，并将其包含在对虾文档中。我们返回 Base64 编码的 PDF。

如果您想创建一个文件，您可以将结果写入文件:

```
base64_gifs = ['R0lGODlhAQABAIAAAAUEBAAAACwAAAAAAQABAAACAkQBADs=']
File.open "your_pdf_document.pdf", "wb" do |file|
  file.write Base64.decode64(Base64ImageToPdfConverter.new(base64_images: base64_gifs)
                                                      .base64_pdf)
  file.close
end 
```

Enter fullscreen mode Exit fullscreen mode

感谢[罗杰](https://github.com/rogercampos/)对 PR 的审查，并使这个类比它的第一次迭代:D 好一点