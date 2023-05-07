# Proyecto Final 

### Integrantes:

- Paula Campaña Donoso: 00215572
- Nicolas Moina: 00321192
- Alex Perez: 00329229
- Sebastian Navarro: 00321588

### Introducción

El objetivo del proyecto es implementar una estructura de datos conocida como Hash maps para el conteo de letras en un extracto de texto, con el fin de desarrollar una solución eficiente y escalable para el análisis de texto y la frecuencia de letras. Para lograr esto, se utilizará un programa de ensamblador en el lenguaje MIPS para implementar un Hash map, una estructura de datos que permitirá realizar la clasificación y el conteo de las letras de manera rápida y eficiente donde la clave será la letra en su equivalente ASCII y el valor la frecuencia.

Se realizarán pruebas exhaustivas para asegurar la correcta funcionalidad del Hash map en diferentes escenarios. El resultado final del proyecto será un programa de ensamblador MIPS que implemente un Hash map eficiente para el conteo de palabras en una extracto de texto. Esta implementación proporcionará una herramienta útil y eficiente para el análisis de texto.

### Codigo en Assembly

Inicialmente se realizó .asm que en este caso son los que contienen el código del hashmap y el código que realiza la conversion de números decimales a código ASCII, a continuación se muestra estos dos archivos. 

Archivo que contiene el código del hashmap

```
.data
hashmap: .space 400       # 100 posiciones de almacenamiento, cada una con 4 bytes (100 * 4 = 400 bytes)
hashmap_size: .word 100
key_value_separator: .asciiz ": "
pair_separator: .asciiz ", "

.text
.globl main

main:
    # Inicializar la tabla hash con NULL (0)
    lw $t0, hashmap_size    # Cargar el tamaño del hashmap en $t0
    li $t1, 0               # Contador de índice
    la $t2, hashmap         # Dirección base del hashmap
init_loop:
    sw $zero, 0($t2)        # Inicializar la posición actual con NULL (0)
    addi $t1, $t1, 1        # Incrementar el contador de índice
    addi $t2, $t2, 4        # Avanzar a la siguiente posición en la tabla hash
    blt $t1, $t0, init_loop

    # Ejemplo de uso de la función put:
    li $a0, 1          # Clave
    li $a1, 50         # Valor
    jal put            # Llamada a la función put

    # Ejemplo de uso de la función put:
    li $a0, 3          # Clave
    li $a1, 3          # Valor
    jal put            # Llamada a la función put

    # Ejemplo de uso de la función put:
    li $a0, 4          # Clave
    li $a1, 20         # Valor
    jal put            # Llamada a la función put

    # Ejemplo de uso de la función get:
    li $a0, 1          # Clave
    jal get            # Llamada a la función get

    # Imprimir el resultado
    move $a0, $v0      # Mover el valor a imprimir en $a0
    li $v0, 1          # Syscall para imprimir entero
    syscall

    # Imprimir un salto de línea
    li $v0, 11         # Syscall para imprimir un caracter
    li $a0, '\n'       # Cargar el caracter de nueva línea
    syscall

   # Guardar el valor de $t1 antes de llamar a print_hashmap
   add $s0, $t1, $zero

   # Imprimir el HashMap
   jal print_hashmap  # Llamada a la función print_hashmap

   # Restaurar el valor de $t1 después de llamar a print_hashmap
   add $t1, $s0, $zero

    # Salir del programa
    li $v0, 10         # Syscall para salir
    syscall

# Imprime el hashmap
# Imprime el hashmap
print_hashmap:
    lw $t0, hashmap_size    # Cargar el tamaño del hashmap en $t0
    la $t1, hashmap         # Dirección base del hashmap
    li $t2, 0               # Contador de índice
    
print_loop:
    beq $t2, $t0, print_done   # Si el contador llega al tamaño del hashmap, salir del bucle
    
    # Imprimir el índice
    move $a0, $t2             # Cargar el índice en $a0
    li $v0, 1                # Syscall para imprimir entero
    syscall

    # Imprimir el separador de clave-valor
    la $a0, key_value_separator
    li $v0, 4                # Syscall para imprimir una cadena
    syscall

    # Imprimir el valor almacenado
    lw $a0, ($t1)             # Cargar el valor almacenado en $a0
    li $v0, 1                # Syscall para imprimir entero
    syscall

    # Imprimir el separador de pares
    la $a0, pair_separator
    li $v0, 4                # Syscall para imprimir una cadena
    syscall

    addi $t1, $t1, 4         # Avanzar a la siguiente posición en la tabla hash
    addi $t2, $t2, 1         # Incrementar el contador de índice
    j print_loop
    
print_done:
    jr $ra                   # Retornar al llamador

# Inserta un par clave-valor en el hashmap
put:
    move $t3, $a0            # Copiar la clave a $t3
    sll $t3, $t3, 2          # Multiplicar la clave por 4 para obtener el desplazamiento
    la $t1, hashmap          # Dirección base del hashmap
    add $t1, $t1, $t3        # Calcular la dirección de memoria del par clave-valor
    
    sw $a1, ($t1)            # Almacenar el valor en la posición correspondiente del hashmap
    
    jr $ra                   # Retornar al llamador


# Obtiene el valor asociado a una clave del hashmap
get:
    move $t3, $a0            # Copiar la clave a $t3
    sll $t3, $t3, 2          # Multiplicar la clave por 4 para obtener el desplazamiento
    la $t1, hashmap          # Dirección base del hashmap
    add $t1, $t1, $t3        # Calcular la dirección de memoria del par clave-valor
    
    lw $v0, ($t1)            # Cargar el valor almacenado en la posición correspondiente del hashmap
    
    jr $ra                   # Retornar al llamador


```

Archivo que contiene el código de la conversion de números decimales a código ASCII

```
    .data
str:    .asciiz "alex perez"         # La cadena de entrada

    .text
main:
    la $t0, str               # Cargamos la dirección de la cadena en $t0
    li $t1, 0                 # Inicializamos el contador en 0
loop:
    lb $t2, ($t0)             # Cargamos un byte de la cadena en $t2
    beqz $t2, exit            # Si encontramos el carácter nulo, salimos del loop
    addi $t0, $t0, 1          # Incrementamos el puntero a la cadena
    addi $t1, $t1, 1          # Incrementamos el contador de caracteres
    jal ascii                 # Saltamos a la función "ascii"
    b loop                    # Volvemos al inicio del loop
exit:
    jr $ra                    # Retornamos al llamador

# Función "ascii"
ascii:
    li $v0, 1                 # Código del syscall para imprimir un entero
    move $a0, $t2             # Pasamos el byte cargado a $a0
    syscall                   # Imprimimos el número ASCII
    li $v0, 4                 # Código del syscall para imprimir un string
    la $a0, sep               # Cargamos la dirección de la coma en $a0
    syscall                   # Imprimimos la coma
    jr $ra                    # Retornamos al llamador

    .data
sep:    .asciiz ", "           # La coma que usaremos para separar los números ASCII


```

Finalmente haciendo uso de los dos archivos creamos uno que realiza las dos tareas en conjunto en este caso la conversion de las letras a código ASCII y posteriormente guarda el numero de veces que se repite cada letra en el hashmap.

```

.data
hashmap: .space 512       # 100 posiciones de almacenamiento, cada una con 4 bytes (100 * 4 = 400 bytes)
hashmap_size: .word 128
key_value_separator: .asciiz ": "
pair_separator: .asciiz ", "
input_string: .asciiz "aalex"

.text
.globl main

main:
    # Inicializar la tabla hash con NULL (0)
    lw $t0, hashmap_size    # Cargar el tamaño del hashmap en $t0
    li $t1, 0               # Contador de índice
    la $t2, hashmap         # Dirección base del hashmap
init_loop:
    sw $zero, 0($t2)        # Inicializar la posición actual con NULL (0)
    addi $t1, $t1, 1        # Incrementar el contador de índice
    addi $t2, $t2, 4        # Avanzar a la siguiente posición en la tabla hash
    blt $t1, $t0, init_loop

    # Leer la cadena de texto y convertir a valores ASCII
    la $t0, input_string    # Dirección de la cadena de entrada

process_string:
    lb $t1, 0($t0)          # Cargar el siguiente caracter de la cadena
    beqz $t1, print_map     # Si el caracter es NULL, terminar el proceso

    # Incrementar el valor en el hashmap para el valor ASCII
    move $a0, $t1           # Clave (valor ASCII)
    jal get                 # Obtener el valor actual del hashmap
    addi $v0, $v0, 1        # Incrementar el valor en 1
    move $a1, $v0           # Mover el nuevo valor a $a1
    jal put                 # Guardar el nuevo valor en el hashmap

    addi $t0, $t0, 1        # Avanzar al siguiente caracter de la cadena
    j process_string

print_map:
    # Guardar el valor de $t1 antes de llamar a print_hashmap
    add $s0, $t1, $zero

    # Imprimir el HashMap
    jal print_hashmap  # Llamada a la función print_hashmap

    # Restaurar el valor de $t1 después de llamar a print_hashmap
    add $t1, $s0, $zero

    # Salir del programa
    li $v0, 10         # Syscall para salir
    syscall
    
# Imprime el hashmap
print_hashmap:
    lw $t0, hashmap_size    # Cargar el tamaño del hashmap en $t0
    la $t1, hashmap         # Dirección base del hashmap
    li $t2, 0               # Contador de índice
    
print_loop:
    beq $t2, $t0, print_done   # Si el contador llega al tamaño del hashmap, salir del bucle
    
    # Imprimir el índice
    move $a0, $t2             # Cargar el índice en $a0
    li $v0, 1                # Syscall para imprimir entero
    syscall

    # Imprimir el separador de clave-valor
    la $a0, key_value_separator
    li $v0, 4                # Syscall para imprimir una cadena
    syscall

    # Imprimir el valor almacenado
    lw $a0, ($t1)             # Cargar el valor almacenado en $a0
    li $v0, 1                # Syscall para imprimir entero
    syscall

    # Imprimir el separador de pares
    la $a0, pair_separator
    li $v0, 4                # Syscall para imprimir una cadena
    syscall

    addi $t1, $t1, 4         # Avanzar a la siguiente posición en la tabla hash
    addi $t2, $t2, 1         # Incrementar el contador de índice
    j print_loop
    
print_done:
    jr $ra                   # Retornar al llamador

# Inserta un par clave-valor en el hashmap
put:
    move $t3, $a0            # Copiar la clave a $t3
    sll $t3, $t3, 2          # Multiplicar la clave por 4 para obtener el desplazamiento
    la $t1, hashmap          # Dirección base del hashmap
    add $t1, $t1, $t3        # Calcular la dirección de memoria del par clave-valor
    
    sw $a1, ($t1)            # Almacenar el valor en la posición correspondiente del hashmap
    
    jr $ra                   # Retornar al llamador


# Obtiene el valor asociado a una clave del hashmap
get:
    move $t3, $a0            # Copiar la clave a $t3
    sll $t3, $t3, 2          # Multiplicar la clave por 4 para obtener el desplazamiento
    la $t1, hashmap          # Dirección base del hashmap
    add $t1, $t1, $t3        # Calcular la dirección de memoria del par clave-valor
    
    lw $v0, ($t1)            # Cargar el valor almacenado en la posición correspondiente del hashmap
    
    jr $ra                   # Retornar al llamador


```


### Conclusion 

Una vez terminado el proyecto, podemos concluir que assembly es un lenguaje en el cual se debe de ser bastante minucioso y cuidadoso con los detalles, ya que un error en el código puede causar que el programa no funcione correctamente. Además, assembly es un lenguaje que requiere bastante tiempo para poder entenderlo y programarlo, ya que es un lenguaje que no es tan intuitivo como otros lenguajes de programación. Por otro lado, assembly puede ser muy útil para poder realizar programas que requieran de un control total sobre el hardware.

Nuestro proyecto se enfocó en implementar un hash map, el cual nos permitio trabajar en distintas areas como por ejemplo: eficiente de almacenamiento, optimización de tiempo de acceso y comprensión de las estructuras de datos. 

En la eficiencia de almacenamiento se pudo observar que el hash map nos permite almacenar y organizar de forma eficiente grandes conjuntos de datos utilizando técnicas de hashing. Un hash map permite acceder rapidamente a los valores almacenados a traves de claves únicas, lo que puede resultar en una mejora significativa en el rendimiento y la eficiencia en comparación con otras estructuras de datos.

Por otro lado en la optimizacion de tiempo de acceso se pudo observar que el uso de una funcion de hash adecuada en la implementacion del hash map puede ayudar a minimizar el tiempo de acceso a los elementos almacenados, buscar y recuperar los datos de manera rapida.

Por último comprendimos mejor la estructura de datos, hash map, ya que pudimos organizar de mejor manera los datos en la memoria. Además de esto, logramos que funcionara de manera correcta y eficiente, lo cual nos permitió mejorar nuestro conocimiento sobre las estructuras de datos y su aplicación en proyectos de programacion.
