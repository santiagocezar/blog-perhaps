---
title: 'Listas en C'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Jul 13 2023'
heroImage: '/listas/thumb.jpg'
---

HOLA ACÁ VOY A EXPLICAR SOBRE LISTAS ENLAZADAS EN C

## ¿Por qué listas enlazadas?

En C, todas las **variables** que usa nuestro programa se guardan en la memoria de forma **secuencial**, ya sean ints, strings, structs o arrays. Cada variable ocupa cierto **espacio** y cuando compilamos el código se calcula **cuanta memoria** usará cada ~~módulo~~ función **en total**.

*Lo cual es un problema.*

En demasiadas ocaciones se da que no tenemos idea de **cuanto espacio** vamos a necesitar para guardar los datos. Por suerte, en C existen funciones que nos permiten **manejar memoria de forma dinámica**, como lo sería la función `malloc()` y `free()`.

Las **listas enlazadas** son *una* forma de usar memoria dinámica, cada elemento se guarda en la memoria en un-lugar-aleatorio-no-sabemos-donde  y tienen la ventaja es que son **muy** eficientes al agregar y borrarlos.

Su desventaja, es que al estar cada elemento un-lugar-aleatorio-no-sabemos-donde, es mucho más dificil (y lento) acceder a un tal n-ésimo elemento.

## Inicializar la lista

Una lista enlazada se compone de **nodos**, cada nodo tiene **un dato** del tipo que necesitemos guardar, y un **puntero** que nos sirve para encontrar el siguiente elemento

```c
struct nodo {
    <tipo del dato> dato;
    struct nodo *sig;
} L, p;
```

> El miembro `sig` es un **puntero** debido a que si **guardaramos el valor** del siguiente en el nodo, también guardariamos el valor del siguiente del siguiente, y tendríamos un nodo adentro de un nodo adentro de un nodo adentro de un nodo... hasta el infinito, y todo lo bueno tiene que tener un final.
 
El puntero es un **número**, nos dice **donde se encuentra en la memoria** el siguiente nodo, y también puede **no apuntar a nada** cuando su valor es `NULL`, esto indica que es el **último elemento de la lista**. 

Para crear el primer nodo, lo creamos con `malloc()`, completamos los datos y guardamos su puntero en inicio

```c
p = (struct nodo*)malloc(sizeof(struct nodo));
p->dato = <valor>;
p->sig = L; // si este es el primero, esto va a ser NULL

L = p;
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
    p = (struct nodo*)malloc(sizeof(struct nodo));
    p->dato = dato;
    
    // 2.
    p->sig = L;
    
    // 3.
    L = p;
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

Para leer los datos simplemente tenemos que ir **avanzando** el puntero `p`. Continuamos hasta que `p` no apunte a nada, es decir, sea `NULL`.

```c
p = L;
while (p != NULL) {
    // hacemos algo con p->dato
    p = p->sig;
}
```

## Eliminar un nodo

Cuando borramos un nodo que no está en el medio, estamos dividiendo la lista en 2 partes, una antes y después del nodo que borramos. Así que hay que asegurarse de **volver a unir** el nodo **anterior** y **posterior** al nodo que eliminamos.

Para esto tenemos que saber cual es el nodo **anterior** al que deseamos borrar, lo guardamos en una variable.


```c
void borrar_primero() {
    p = L;
    L = p->sig;
    free(p);
}
void borrar_actual() {
    anterior->sig = p->sig;
    free(p);
}
void borrar_elemento(<tipo de dato> dato) {
    if (L->dato == dato) { // el primero es un caso especial
        borrar_primero();
    } else {
        p = L->sig; // ya sabemos que el primero no es, next!
        while (p != NULL && p->dato != v) {
            anterior = p;
            p = p->sig;
        }
        if (p != NULL) { // va a ser NULL cuando no exista el elemento
            borrar_actual();
        }
    }
}
```
![](/listas/free.png)

El **primer elemento** se trata como un caso especial porque **no tiene un nodo anterior**, y además hay que actualizar el puntero `L` para que no apunte a la nada cuando lo borremos.

## Insertar un nodo después de otro

En el caso de que queramos agregar nuesto elemento después de `p`, podemos usar esta función:

```c
void insertar(<tipo de dato> dato) {
    nuevo = (struct nodo*)malloc(sizeof(struct nodo));
    nuevo->dato = dato;

    nuevo->sig = p->sig; 
    p->sig = nuevo; 
}
```

Si es una lista ordenada, podemos usarla junto a `anteponer()` cuando es más chico que el primer elemnto:

```c
void insertar_ordenado(int dato) {
    if(L == NULL || dato < L->dato) {
        anteponer(dato);
    } else {
        p = L;
        while(p->sig != NULL && dato >= p->sig->dato) {
            p = p->sig;
        }
        insertar(dato)
    }
}
```