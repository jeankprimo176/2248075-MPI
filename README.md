# 2248075-MPI

# ej 1

1. Codigo en C

```
/*
 * C Program to Perform Binary Search using Recursion
 */
 
#include <stdio.h>
 
void binary_search(int [], int, int, int);
void bubble_sort(int [], int);
 
int main()
{
    int key, size, i;
    int list[25];
 
    printf("Enter size of a list: ");
    scanf("%d", &size);
    printf("Enter elements\n");
    for(i = 0; i < size; i++)
    {
        scanf("%d",&list[i]);
    }
    bubble_sort(list, size);
    printf("\n");
    printf("Enter key to search\n");
    scanf("%d", &key);
    binary_search(list, 0, size, key);
 
}
 
void bubble_sort(int list[], int size)
{
    int temp, i, j;
    for (i = 0; i < size; i++)
    {
        for (j = i; j < size; j++)
        {
            if (list[i] > list[j])
            {
                temp = list[i];
                list[i] = list[j];
                list[j] = temp;
            }
        }
    }
}
 
void binary_search(int list[], int lo, int hi, int key)
{
    int mid;
 
    if (lo > hi)
    {
        printf("Key not found\n");
        return;
    }
    mid = (lo + hi) / 2;
    if (list[mid] == key)
    {
        printf("Key found\n");
    }
    else if (list[mid] > key)
    {
        binary_search(list, lo, mid - 1, key);
    }
    else if (list[mid] < key)
    {
        binary_search(list, mid + 1, hi, key);
    }
}
```
En la que tenemos el siguiente par de funciones:

*binary_search(int [], int, int, int): Esta función realiza la búsqueda binaria en un arreglo de enteros. Toma cuatro argumentos: el arreglo en el que se va a buscar, los límites inferiores y superiores del segmento del arreglo actual y la clave que se está buscando.

*bubble_sort(int [], int): Esta función implementa el algoritmo de ordenamiento de burbuja para ordenar el arreglo en orden ascendente antes de realizar la búsqueda binaria.



2. Adaptación del Código: Vamos a modificar ligeramente el código para que funcione en un entorno MPI.

```
#include <stdio.h>
#include <mpi.h>

void binary_search(int [], int, int, int);
void bubble_sort(int [], int);

int main()
{
    int key, size, i;
    int list[] = {2, 4, 7, 10, 11, 45, 50, 59, 60, 66, 69, 70, 79};
    size = sizeof(list) / sizeof(list[0]);

    bubble_sort(list, size);

    key = 66; // Definimos la clave directamente aquí

    MPI_Init(NULL, NULL);
    binary_search(list, 0, size, key);
    MPI_Finalize();

    return 0;
}

void bubble_sort(int list[], int size)
{
    int temp, i, j;
    for (i = 0; i < size; i++)
    {
        for (j = i; j < size; j++)
        {
            if (list[i] > list[j])
            {
                temp = list[i];
                list[i] = list[j];
                list[j] = temp;
            }
        }
    }
}

void binary_search(int list[], int lo, int hi, int key)
{
    int mid;
    int rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    if (lo > hi)
    {
        if (rank == 0) {
            printf("Key not found\n");
        }
        return;
    }
    mid = (lo + hi) / 2;
    if (list[mid] == key)
    {
        if (rank == 0) {
            printf("Key found at index %d\n", mid);
        }
    }
    else if (list[mid] > key)
    {
        binary_search(list, lo, mid - 1, key);
    }
    else if (list[mid] < key)
    {
        binary_search(list, mid + 1, hi, key);
    }
}
```

En esta adaptación del código original a MPI, se han realizado los siguientes cambios:

*Inclusión de la biblioteca MPI:
Se incluye la biblioteca <mpi.h> para utilizar las funciones y estructuras de datos proporcionadas por MPI.

*Inicialización y finalización de MPI:
Se llama a MPI_Init(NULL, NULL) al comienzo del programa para inicializar el entorno MPI.
Se llama a MPI_Finalize() al final del programa para finalizar el entorno MPI.

*Uso de MPI_Comm_rank:
Se utiliza MPI_Comm_rank(MPI_COMM_WORLD, &rank) para obtener el identificador de rango del proceso actual en el comunicador predeterminado MPI_COMM_WORLD.

*Impresión de resultados:
La impresión de resultados se ha modificado para que solo el proceso con rango 0 imprima el resultado. Esto se logra con una condición if (rank == 0) alrededor de las impresiones.

*Definición de la clave de búsqueda:
La clave de búsqueda se ha definido directamente en el código como key = 66;.

*Eliminación de entrada de usuario:
La entrada de tamaño del arreglo y de los elementos del arreglo se han eliminado. En cambio, se ha definido un arreglo de enteros list con valores predefinidos para simplificar el ejemplo.



