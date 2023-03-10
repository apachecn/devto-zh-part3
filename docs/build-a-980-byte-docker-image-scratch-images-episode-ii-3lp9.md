# 构建一个 980 字节的 Docker 映像- Scratch Images 第二集

> 原文：<https://dev.to/tonymet/build-a-980-byte-docker-image-scratch-images-episode-ii-3lp9>

📓[要旨](https://gist.github.com/tonymet/a80595400b8481e8a0274d63c290ceec)

为什么停在 [100kB](https://dev.to/tonymet/build-100kb-docker-images-from-scratch-4ll5) ？我们能制作的最小的、可运行的 docker 映像是什么？

## hello.asm

`hello.asm`

```
; Define variables in the data section
SECTION .DATA
    hello:     db 'Hello world!',10
    helloLen:  equ $-hello

; Code goes in the text section
SECTION .TEXT
    GLOBAL _start 

_start:
    mov eax,4            ; 'write' system call = 4
    mov ebx,1            ; file descriptor 1 = STDOUT
    mov ecx,hello        ; string to write
    mov edx,helloLen     ; length of string to write
    int 80h              ; call the kernel

    ; Terminate program
    mov eax,1            ; 'exit' system call
    mov ebx,0            ; exit with error code 0
    int 80h              ; call the kernel 
```

`Makefile`

```
 hello.o: hello.asm
    nasm -f elf64 $< -o $@

hello: hello.o
    ld $< -o $@ 
```

## docker file

```
FROM alpine:latest as builder
WORKDIR /build
RUN apk update && \
    apk add nasm make binutils
COPY .  ./
RUN make hello

FROM scratch
WORKDIR /
COPY --from=builder /build/hello .
CMD ["/hello"] 
```

基本上和以前一样，只是新的`apk` deps 为`nasm`。

## 只有 980 字节！

```
docker images |grep tiny-hello|awk '{print $8}'
952B 
```

## 接下来是什么？

我们将构建一个更加可行的 REST API，从一个典型的 docker 映像中剔除 99%的内容。这是一个更短的例子，因为它与最后一个的[基本相同。](https://dev.to/tonymet/build-100kb-docker-images-from-scratch-4ll5)

➡️在那之前，你能打败 980 字节吗？

非常感谢我的朋友和 devops 专家 Philippe 和这个 [hello world 汇编器](https://www.devdungeon.com/content/hello-world-nasm-assembler)鼓励我变小。