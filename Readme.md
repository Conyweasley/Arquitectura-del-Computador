# Laboratorio ARMv8 en SystemVerilog
El laboratorio está basado en la implementación del microprocesador ARMv8 en una versión reducida.

## Objetivos
* Desarrollar códigos en lenguaje SystemVerilog para describir circuitos secuenciales y combinacionales vistos en el teórico y el práctico.
* Utilizar la herramienta Quartus para analizar y sintetizar el código SystemVerilog.
* Aprender a reutilizar código SystemVerilog mediante módulos estructurales.
* Mediante el uso de test bench, analizar las formas de onda y testear los resultados.
* Aplicar los conceptos aprendidos sobre microprocesadores y la técnica de mejora de
rendimiento: segmentación de cauce (pipeline).

# Estructura
La estructura del procesador es la siguiente:
![](processor_arm.png)
![](Esquemadeldatapath.png)

# Ejercicios
Ver como se resolvieron los primeros 3 ejercicios [aquí](Laboratorio%201/README.md). Los otros 3 ejercicios [aquí](Laboratorio%202%3A%20Aplicaci%C3%B3n%20Servidor/Informe.md)
## Ejercicio 1
Sin afectar el funcionamiento de las instrucciones ya implementadas en la versión reducida
del microprocesador con pipeline, agregar las instrucciones **ADDI y SUBI**. Introducir en el
procesador todas las modificaciones necesarias, tanto en el datapath como en las señales
de control. Testear estas funciones. Es decir, se debe analizar que todo el set de instrucciones continúe
funcionando correctamente y también las instrucciones tipo I agregadas (para esto pueden
escribir los resultados en memoria y verificar si obtienen los valores correctos en
“mem.dump” al finalizar la ejecución del código). No olvidar resolver los eventuales
problemas de hazard agregando instrucciones “nop”.

## Ejercicio 2
Sin afectar el funcionamiento de las instrucciones ya implementadas en el ejercicio 1, agregar las instrucciones de saltos
condicionales **B.cond** y las instrucciones aritméticas que configuran las banderas del
microprocesador: **ADDS, SUBS, ADDIS y SUBIS**. Estas últimas instrucciones son necesarias para que los saltos condicionales (B.cond) puedan determinar si deben saltar o no.
Para que este ejercicio sea considerado correctamente implementado, el microprocesador
debe cumplir los siguientes requerimientos:

* La ALU debe generar las 4 banderas existentes en el microprocesador estudiado: Zero,
Negative, Carry y oVerflow. Además debe generar la señal write_flags que indica si se
deben actualizar las banderas o no.
* En las nuevas instrucciones ADDS, SUBS, ADDIS y SUBIS la señal alucontrol debe ser
similar al de las ADD, SUB, ADDI y SUBI (respectivamente) pero con un ‘1’ en el bit más
significativo.
* Debe existir un registro de flags llamado CPSR_flags, de cuatro bits, que almacene el
estado de las banderas (Z, N, C, V). Este registro debe escribirse únicamente cuando se
ejecuta una instrucción que setee flags (ADDS, SUBS, ADDIS o SUBIS).
* Debe crearse una nueva señal de control llamada condBranch que indica si se debe
realizar un salto condicional.
* Se deben agregar las 14 condiciones de saltos existentes en el set de instrucciones
LEGv8. El salto condicional es una instrucción tipo CB. En los cinco bits menos
significativos se indica qué condición se debe cumplir para que se tome el salto de la forma
correcta.
* Todas las demás instrucciones, incluida la de salto CBZ, deben continuar funcionando
normalmente.

## Ejercicio 3
El procesador LEGv8 desarrollado no tiene la capacidad de detectar la ocurrencia de
hazards de ningún tipo. En este ejercicio se propone la implementación de un bloque de
detección de hazards (Hazard Detection Unit) y otro de forwarding (Forwarding Unit), a fin
de aplicar la técnica de forwarding-stall en caso de la ocurrencia de un data hazard, hasta
que el mismo desaparezca.
Algunas aclaraciones respecto a la implementación:
*  La HDU debe implementarse en la instancia del Instruction decode (ID).
* Las diversas condiciones para la detección de un data hazard se analizan en el
capítulo 4.7- “Data Hazards: Forwarding vs Stalling” del libro “Computer Organization
and Design - ARM Edition” de D.Patterson y J. Hennessy. Se deben considerar
TODAS las condiciones para todos los tipos de dependencias de datos.
* Para generar la condición de stall en el procesador es necesario:
    * Evitar que el PC avance a la siguiente instrucción en el siguiente CLK y evitar
que el registro de pipeline IF/ID cambie de valor en el siguiente CLK
(congelar su valor). Para esto deberán diseñar una nueva entidad FLOPRE
similar al FLOPR, pero agregando una señal de enable (habilitación).
Funcionamiento: enable = 1 el funcionamiento es normal, enable = 0 no
altera el valor de salida al detectar un flanco de CLK (síncrono).
    * Forzar que todas las señales de control a partir del ciclo EX en adelante
tomen el valor “0” (Ver implementación de referencia en Fig 4.59).
* ¡No olvidar que una parte del registro IF/ID está en la entidad #processor_arm
