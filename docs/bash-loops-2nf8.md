# Bash:循环

> 原文：<https://dev.to/adzubla/bash-loops-2nf8>

bash 中不同的循环风格:

```
for i in $( ls )
do
    echo $i
done 
```

```
for i in $( seq 1 10 )
do
    echo $i
done 
```

```
for ((i = 0 ; i < max ; i++ ))
do
    echo $i
done 
```

```
i=0
while [ $i -lt 10 ]
do
    echo $i
    i=$((i+1))
done 
```

```
i=20
until [ $i -lt 10 ]
do
    echo $i
    i=$((i-1))
done 
```