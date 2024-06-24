# Php

## Operadores lógicos (`1_operadores_logicos.php`)

| nombre | ejemplo | resultado |
|---|---|---|
| `$valorA and $valorB` | and | `true` si valorA y valorB son `true` |
| `$valorA or $valorB` | or | `true` si valorA o valorB son `true` |
| `$valorA xor $valorB` | xor | `true` si uno de los dos valores son `true`, pero no ambos |
| `!$valorA` | not | `true` si valorA es `false` |
| `$valorA && $valorB` | and | `true` si valorA y valorB son `true` |
| `$valorA \|\| $valorB` | or | `true` si valorA o valorB son `true` |

`var_dump` nos muestra el tipo de dato de una variable.

    $resultado = true and true;
    var_dump($resultado)

    > bool(true)

## Operadores aritméticos (`2_operadores_aritmeticos.php`)

| nombre | ejemplo | resultado |
|---|---|---|
| `$valorA + $valorB` | suma | La suma de valorA y valorB |
| `$valorA - $valorB` | resta | La diferencia de valorA y valorB |
| `$valorA * $valorB` | multiplicación | La multiplicación de valorA y valorB |
| `$valorA / $valorB` | división | La división de valorA entre valorB |
| `$valorA % $valorB` | módulo | El remanente de la división de valorA y valorB |
| `$valorA ** $valorB` | potenciación | El resultado de elevar valorA a la potencia de valorB |

## Estructuras de control (`3_estructuras_de_control.php`)

Son herramientas que provee el lenguaje para poder hacer validaciones, iteraciones y muchas más cosas de acuerdo a lo que queramos para el proyecto.

### if-else

    if([condicion]) {
        // ejecuta si la condicion se cumple
    } else {
        // ejecuta si ninguna condición se cumple
    }

### while

    while([condicion]) {
        // se ejecuta mientras la condicion sea verdadera
    }

### do-while

    do {
        // se ejecuta mientras la condicion sea verdadera
    } while ([condicion]);

### for

    for([valor inicial]; [condicion]; [final de ejecucion]) {
        // se ejecuta mientras la condicion se cumpla
        // se define un valor inicial y un final de ejecucion para que la condicion se rompa y no se vuelva un ciclo infinito
    }

### foreach

    foreach([iterable] as [iteracion]) {
        // ejecuta mientras hayan valores en el iterable
    }

### switch

    switch([variable a evaluar]) {
        case [condicion]:
            // ejecuta si la condicion es verdadera
        break;
        default:
            // ejecuta si ninguna condidion se cumple
        break;
    }

## Funciones (`4_funciones.php`)

Una función se encarga de realizar una acción muy especifica y puntual dentro de los scripts.

    function [nombre_funcion] (parametro_1, parametro_2) {
        // se ejecuta cuando la funcion sea invocada
    }

[**Documentación**](php.net/manual/es/index.php)

## Mostrando datos HTML desde PHP

Debido a que PHP es un lenguaje enfocado en web tiene la facilidad de poderse ejecutar desde el navegador y poder hacer interacciones como un formulario.
