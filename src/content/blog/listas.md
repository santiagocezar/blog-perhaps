---
title: 'Listas en C'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Jul 08 2022'
heroImage: '/blog-placeholder-3.jpg'
---

HOLA ACÁ VOY A EXPLICAR SOBRE LISTAS ENLAZADAS EN C

## ¿Por qué listas enlazadas?

En C, todas las **variables** que usa nuestro programa se guardan en la memoria de forma **secuencial**, ya sean ints, strings, structs o arrays. Cada variable ocupa cierto **espacio** y cuando compilamos el código se calcula **cuanta memoria** usará cada ~~módulo~~ función **en total**.

*Lo cual es un problema.*

En demasiadas ocaciones se da que no tenemos idea de **cuanto espacio** vamos a necesitar para guardar los datos. Por suerte, en C existen funciones que nos permiten **reservar memoria de forma dinámica**, como lo sería la función `malloc()`.

Las **listas enlazadas** son *una* forma de usar memoria dinámica, cada elemento se guarda en la memoria en un-lugar-aleatorio-no-sabemos-donde  y tienen la ventaja es que son **muy** eficientes al agregar y borrarlos.

Su desventaja, es que al estar cada elemento un-lugar-aleatorio-no-sabemos-donde, es mucho más dificil (y lento) acceder a un tal n-ésimo elemento.

## Inicializar la lista

Una lista enlazada se compone de **nodos**, cada nodo tiene **un dato** del tipo que necesitemos guardar, y un **puntero** que nos sirve para encontrar el siguiente elemento

```c
struct nodo {
    <tipo del dato> dato;
    struct nodo *siguiente;
} inicio, temporal; // inicio sería L, temporal sería p
```

> El miembro `siguiente` es un **puntero** debido a que si **guardaramos el valor** del siguiente en el nodo, también guardariamos el valor del siguiente del siguiente, y tendríamos un nodo adentro de un nodo adentro de un nodo adentro de un nodo... hasta el infinito, y todo lo bueno tiene que tener un final.
 
El puntero es un **número**, nos dice **donde se encuentra en la memoria** el siguiente nodo, y también puede **no apuntar a nada** cuando su valor es `NULL`, esto indica que es el **último elemento de la lista**. 

Para crear el primer nodo, lo creamos con `malloc()`, completamos los datos y guardamos su puntero en inicio

```c
temporal = (struct nodo*)malloc(sizeof(struct nodo));
temporal->dato = <valor>;
temporal->siguiente = inicio; // si este es el primero, esto va a ser NULL

inicio = temporal;
```

> `sizeof` nos da el **tamaño** del struct, le pasamos ese valor a `malloc` para que **reserve un lugar en la memoria** donde podamos meterlo. `malloc` devuelve un **puntero comodín** (`void*`), y tenemos que **convertirlo** en un `struct nodo*`.

## Agregar al principio

*Por la naturaleza de las listas vinculadas* (?), resulta más facil agregar datos al principio que al final, es más, el código es idéntico al bloque de arriba:
1. Creamos y guardamos los datos en el nodo
2. Ubicamos al nuevo nodo **antes** que el primer nodo **actual**
3. Indicamos que el primer nodo **es** el nuevo nodo que agregamos

```c
void anteponer(<tipo de dato> dato) {
    // 1. 
    temporal = (struct nodo*)malloc(sizeof(struct nodo));
    temporal->dato = dato;
    
    // 2.
    temporal->siguiente = inicio;
    
    // 3.
    inicio = temporal;
}
```


> Los datos quedan en el **orden inverso** al que los agregamos, si tuvieramos el siguiente código:
> 
> ```c
> void anteponer(int dato);
> // ...
> for (int i = 0; i < 5; i++) {
>     anteponer(i);
> }
> ```
> al leer y imprimir los datos nos tiraría `4 3 2 1 0`.

## Leer los datos

Para leer los datos simplemente tenemos que ir **avanzando** el puntero `temporal`. Continuamos hasta que `temporal` no apunte a nada, es decir, sea `NULL`.

```c
temporal = inicio;
while(temporal != NULL) {
    // hacemos algo con temporal->dato
    temporal = temporal->siguiente;
}
```

