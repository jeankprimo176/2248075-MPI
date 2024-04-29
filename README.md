# 2248075-MPI

# ej 1

1. Codigo en C original

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
La entrada de tamaño del arreglo y de los elementos del arreglo se han eliminado. En cambio, se ha definido un arreglo de enteros list[] = {2, 4, 7, 10, 11, 45, 50, 59, 60, 66, 69, 70, 79} con valores predefinidos para simplificar el ejemplo.




3.Compilación: 

Primero cargamos el modulo de openmpi

```
[jcportillam@guane ~]$ module load devtools/mpi/openmpi/4.1.2
```

Compilaremos el código para generar el ejecutable.

```
[jcportillam@guane ~]$ nano binary_search_mpi_1.c
[jcportillam@guane ~]$ mpicc binary_search_mpi_1.c -o binary_search_mpi_1
```


Este comando mpicc se utiliza para compilar un programa escrito en C que utiliza la biblioteca MPI:

*mpicc: Es el comando para compilar programas escritos en C que utilizan MPI. Este comando está específicamente diseñado para compilar programas MPI y facilita la vinculación de las bibliotecas MPI necesarias.

*binary_search_mpi_1.c: Es el nombre del archivo fuente que contiene el código en C que implementa la búsqueda binaria utilizando MPI. Este archivo debe estar presente en el directorio actual o se debe proporcionar la ruta completa al archivo.

*-o binary_search_mpi_1: Esta parte del comando indica al compilador que debe generar un archivo de salida llamado binary_search_mpi_1. Este archivo será el ejecutable resultante después de compilar el código fuente proporcionado.

En resumen, este comando compila el programa binary_search_mpi_1.c, que implementa la búsqueda binaria utilizando MPI, y genera un archivo ejecutable llamado binary_search_mpi_1.

4. Creación del Script de Ejecución (.sbatch): Crearemos un script .sbatch para enviar el trabajo al clúster.

```
#!/bin/bash
#SBATCH --job-name=binary_search_mpi_1
#SBATCH --output=binary_search_mpi_1_%j.out
#SBATCH --error=binary_search_mpi_1_%j.err
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=1
#SBATCH --time=00:10:00
#SBATCH --partition=normal

mpirun -np 2 ./binary_search_mpi_1
```

Aquí está el desglose de lo que hace este script:

* #!/bin/bash: Esta línea es conocida como shebang e indica que el script debe ser interpretado por el shell Bash.

* Líneas que comienzan con #SBATCH: Estas líneas son directivas de configuración que se utilizan para especificar los recursos y la configuración del trabajo que se enviará al sistema de gestión de trabajos Slurm. Aquí hay una explicación de cada directiva:

**--job-name=binary_search_mpi_1: Especifica el nombre del trabajo que se está enviando.

**--output=binary_search_mpi_1_%j.out: Indica el nombre del archivo donde se redireccionará la salida estándar del trabajo. %j será reemplazado automáticamente por el ID del trabajo asignado por Slurm.

**--error=binary_search_mpi_1_%j.err: Indica el nombre del archivo donde se redireccionarán los errores estándar del trabajo. %j se reemplazará automáticamente por el ID del trabajo asignado por Slurm.

**--nodes=2: Especifica el número de nodos que se solicitarán para ejecutar el trabajo. En este caso, se solicitan dos nodos.

**--ntasks-per-node=1: Especifica el número de tareas MPI que se ejecutarán en cada nodo. En este caso, se ejecutará una tarea MPI por nodo.

**--time=00:10:00: Especifica el tiempo máximo que se permitirá que el trabajo se ejecute. En este caso, se permite un máximo de 10 minutos.

**--partition=normal: Especifica la partición en la que se ejecutará el trabajo. Las particiones pueden tener diferentes configuraciones y políticas de recursos.

*mpirun -np 2 ./binary_search_mpi_1: Esta línea es el comando que realmente ejecuta el trabajo MPI. mpirun es un comando utilizado para lanzar programas MPI. -np 2 especifica el número de procesos MPI que se crearán para ejecutar el trabajo, en este caso, se solicitan dos procesos MPI. ./binary_search_mpi_1 es el ejecutable del programa MPI que se ejecutará.

5.Ejecución en el Clúster: Usaremos sbatch para enviar el script .sbatch al clúster y ejecutar el programa.

```
[jcportillam@guane ~]$ nano run_binary_search_mpi_1.sbatch
[jcportillam@guane ~]$ sbatch run_binary_search_mpi_1.sbatch
Submitted batch job 62870
[jcportillam@guane ~]$ squeue
     JOBID       PARTITION         NAME       USER  ST         TIME NODE NODELIST(REASON)
     61965          normal     carefopt wacontrera   R  48-18:14:22    1 guane11
     61966          normal    iftrefopt wacontrera   R  48-17:08:29    1 guane11
     61983          normal    zporefopt wacontrera   R  48-00:10:27    1 guane11
     61984          normal    zpmrefopt wacontrera   R  47-23:40:29    1 guane12
     62717             Viz inversion_wi amartinezm   R   4-18:58:40    1 yaje
     62864          normal    neb_ts_c5 ccquijanoc   R     20:12:23    1 guane10
     62865            GIRG          SPH  lmbecerra   R        31:09    1 thor
[jcportillam@guane ~]$ cat binary_search_mpi_1_62870.out
Key found at index 9
```
*nano run_binary_search_mpi_1.sbatch: Este comando abre el editor de texto nano y crea/edita un archivo llamado run_binary_search_mpi_1.sbatch. Presumiblemente, este archivo es un script de lote (batch script) Slurm, similar al que se explicó anteriormente, que describe el trabajo que se enviará al clúster.

*sbatch run_binary_search_mpi_1.sbatch: Este comando envía el trabajo descrito en el archivo run_binary_search_mpi_1.sbatch al sistema de gestión de trabajos Slurm para su ejecución. sbatch es el comando utilizado para enviar trabajos al clúster. Después de enviar el trabajo, Slurm devuelve un número de identificación de trabajo (JOBID) que se utiliza para hacer referencia al trabajo en futuras interacciones con Slurm.

*squeue: Este comando muestra una lista de trabajos actualmente en cola en el clúster. Muestra información como el ID del trabajo, la partición en la que se encuentra, el nombre del trabajo, el usuario que lo envió, el estado actual del trabajo, el tiempo transcurrido desde que se inició y la lista de nodos asignados (si está en ejecución). En este caso, muestra una lista de varios trabajos en ejecución o en cola en diferentes nodos del clúster.

*cat binary_search_mpi_1_62870.out: Este comando muestra el contenido del archivo de salida (binary_search_mpi_1_62870.out) asociado al trabajo con el ID 62870. Presumiblemente, este archivo contiene la salida generada por el programa MPI binary_search_mpi_1 que se ejecutó como parte del trabajo enviado anteriormente. En este caso, muestra el mensaje "Key found at index 9", lo que indica que se encontró una clave en el índice 9 durante la ejecución del programa MPI.
