# 决定性是一个初值

> 原文：<https://dev.to/358b06c6/determinar-si-un-numero-es-primo-pseint-c4i>

我们取一个用户输入的数字，除以它和它之前的所有自然数字。

每次"组织"给我们造成零残馀时我们都会把这一事件记录在一个变量中。如果事件数目正好是两个；这是质数。

```
Algoritmo DeterminarNumerosPrimos
    Definir numeroIngresado, iteracion, divisionResiduoCero Como Entero;

    Escribir "Determinar si un número ingresado, es un número primo.";
    Escribir "Teclea un número entero: ";
    Leer numeroIngresado;

    // Comenzaremos dividiendo el número ingresado entre 1.
    iteracion = 1;

    // Número de ocasiones en las que la división dio como resultado un residuo de cero.
    divisionResiduoCero = 0;

    Mientras iteracion <= numeroIngresado Hacer

        Si(numeroIngresado % iteracion == 0) Entonces
            divisionResiduoCero = divisionResiduoCero + 1;
        FinSi

        iteracion = iteracion + 1;

    FinMientras

    Si(divisionResiduoCero == 2) Entonces
        Escribir "El número que ingresaste es un número primo.";
    SiNo
        Escribir "El número que ingresaste NO es un número primo.";
    FinSi   

FinAlgoritmo 
```