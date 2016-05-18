#Algunas maneras de temporizar  
  
  
##Usando el procesador para pasar el tiemp
Una forma de temporizar acciones es intercalar instrucciones de retardo, sabiendo cuanto tarda cada una (cantidad de ciclos de clock) se pueden ejecutar repetidas veces, y contarlas para calcular el intervalo de tiempo total. La instrucción 'nop' es útil para esto ya que no tiene otros efectos colaterales, más que gastar el tiempo en ejecución. 

Ejemplo en una subrutina de retardo
    
    retardo:	nop 
                nop
                nop				
                return            

Esta no es la mejor manera de temporizar, entre otras cosas porque dedica la ejecución del programa exclusivamente a hacer un retardo, obstaculizando la posible realización de otras tareas.

La subrutina puede llamarse mediante `call retardo`, y tarda el tiempo de tres instrucciones `nop`, hay que sumar además el tiempo de ejecución de las instrucciones `call` y `return`, que son necesarias para hacer el llamado y para volver de la subrutina (call y return).
Además no todas las instrucciones tardan lo mismo, en microcontroladores diferentes las instrucciones pueden requerir diferentes tiempos de ejecución (cantidad de ciclos de clock). Supongamos que las instrucciones para dar saltos (como `goto`, `call` y `return`) tardan 2 ciclos, y que todas las demás ocupan 1 ciclo, el total de ciclos para el ejemplo es 2 del call + 1 nop + 1 nop + 1 nop + 2 del return = 7 ciclos . Ese es el tiempo total que consume la subrutina.

Vemos que sólo sirve para retardos muy pequeños, tiempos cercanos al de una instrucción individual, si bien se podrían agregar más nop's, esto llenaría la memoria de programa, aún con un retardo pequeño! otro problema es que es un tiempo fijo. Podemos mejorar ambos asuntos con un bucle y una variable. 

    retardoBucle:       movlw d'100'            ;W = 100 valor fijo literal al contador inicial
                        movwf contador          ;contador = W
    bucle:              decfsz contador,1       ;contador = contador - 1
                        goto bucle              ;ir a bucle
                        return                  ;volver donde indique el stack

Cada vez que se ejecuta la instrucción **decfsz** descuenta 1 a la variable contador, cuando llega a cero saltea la instrucción siguiente (en este caso goto), terminando así el ciclo y pasando al return.
  
¿Qué tiempo (cantidad de ciclos) tarda `call retardoBucle`? 

Hagamos las cuentas... 

> Empezamos por 2 ciclos del call para el llamado + 1 ciclo para movlw, + 1 ciclo para movwf + (1 ciclo para decfsz + 2 ciclos del bucle) lo cual se repetirá 99 veces y en la vez numero 100 salteará el goto, es decir, 2 ciclos más de ese salto + 2 del return, total = 2+1+1+3\*99+2+2 = 8+99\*3= 305 ciclos. Si quisiéramos un tiempo diferente reutilizando el programa, cambiamos el valor de inicialización de la variable contador, n=100, total = 8+3*(n-1), despejando n, queda n = ( total - 8 ) /3 + 1 = ( total - 5 )/3, para un retardo de 200 ciclos exactos, resulta n = 65. 
En este caso son valores que dan resultado entero, la mayoría de las veces no es fácil hacer un retardo de un número preciso de ciclos. **Esa "fórmula" sólo sirve para este programa particular, cualquier modificación al mismo hace que se tenga que elaborar una nueva!.**

Otra manera más flexible es que el valor inicial pueda variarse, cargándolo previamente en W, pero desde afuera de la subrutina, en cada llamado, antes de hacer el call. 

        retardoFlexible:        movwf contador          ; contador = W, W = ? valor fijado afuera
        bucle:                  decfsz contador,1       ; contador = contador - 1
                                goto bucle              ; ir a bucle
                                return                  ; volver donde indique el stack

Antes de cada call debe inicializarse W:
				
				movlw d'100'            ; 1 ciclo, carga 100 en W 
				call retardoFlexible    ; 305 ciclos   
				
				movlw d'65'             ; 1 ciclo, carga 65 en W 
				call retardoFlexible    ; 200 ciclos 
					
Para calcular el tiempo en segundos basta conocer el tiempo de 1 ciclo, si 1 ciclo = 1uS (microsegundo), entonces 200 ciclos son 200uS, un retardo de 1 milisegundo son 1000 ciclos y un segundo 1000000.
					
El ejemplo usa 1 byte para especificar el tiempo, esto no admite entonces un número mayor a 255; para resolver este problema podemos hacer un bucle que llame a otro bucle, con cuidado de no anidar demasiadas veces porque se podría desbordar la pila/stack.

 
        retardoAnidado:                 movwf contador2 
        bucle2:                         decfsz contador2,1							
                                        goto retardar	
                                        goto salir    					
        retardar:                       call retardoBucle
                                        goto bucle2
        salir:                          return    

Otra vez cuentas... 
> La instrucción`goto salir` podría haberse ahorrado, pero así queda más claro el punto de retorno que está al final de la función. Calculemos, si W viene cargado con el valor n, entonces tenemos 2 ciclos del `call` + 1 ciclo del `movwf` + (1 del decfsz + 2 del `goto` + el retardoBucle + 2 del segundo `goto` ) * (n-1) + 2 ciclos del `decfsz` cuando salta + 2 del `goto salir` + 2 del `return`. Si retardoBucle= 305 y n = 100, tenemos total 1+(5+305)*99 +2 +2 + 2 = 30699 ciclos  

Una forma diferente de hacer retardos es utilizando timers y interrupciones por hardware. 

##Timers

Con temporizadores por hardware se pueden esperar lapsos de tiempo de una manera independiente al programa que se esté ejecutando, contabilizan por hardware cierto número de ciclos sin ejecutar instrucciones. Se incrementan automáticamente cada cierta cantidad de ciclos de clock; el valor actual se puede leer desde el programa en una dirección ubicada en el mapa de memoria tal cual como cualquier variable; por otro lado mediante bits llamados flags "banderas" se puede saber si un temporizador desborda (si contó hasta superar su capacidad, ej. para 8 bits desborda al hacer 255 + 1, y vuelve a cero).
Usualmente a los Timers se les asocia una interrupción que se dispara cuando el temporizador desborda, la interrupción hace que el programa salte automáticamente a un lugar específico del código, permitiendo que el microcontrolador "avise" cuando terminó de contar, sin necesidad de andar chequeando el flag continuamente para saber "cuándo" desborda.
 
La cuenta del timer se puede incrementar con los ciclos internos de clock, o con una señal externa, la relación de conteo puede ser 1:1, un conteo por ciclo, o puede configurarse otra mediante los llamados prescalers, 1:2 (incrementa uno cada dos ciclos), 1:4 (incrementa uno cada cuatro ciclos), etc.. así se puede cambiar la duración de cada cuenta y por tanto el tiempo total que tarda en desbordar.

El microcontrolador PIC16F628 trae 6 temporizadores por hardware, 4 pueden ser accedidos mediante software y dos de uso interno :

De uso interno

	• Power-Up Timer (PWRT)
	• Oscillator Start-Up Timer (OST)
	
Accesibles por software

	• Watchdog Timer (WDT)	
	• Timer0: 8-bit timer/counter with 8-bit programmable prescaler
	• Timer1: 16-bit timer/counter with external crystal/clock capability
	• Timer2: 8-bit timer/counter with 8-bit period register, prescaler and postscaler
	
###Power Up Timer (PWRT)

Este temporizador produce un retardo de 72 ms desde que se le aplica alimentación al microcontrolador hasta que empieza a correr el programa; durante este tiempo el microcontrolador es mantenido en un estado de reset. La función es esperar que se estabilice la tensión de alimentación ante posibles transitorios. Este timer puede ser habilitado o deshabilitado mediante la configuración en el momento de la programación. No es accesible en tiempo de ejecución.

###Oscillator Start-Up Timer (OST)

Este temporizador provee 1024 ciclos de oscilador de retardo y comienza a andar después de la finalización del PWRT o luego de un reset. La función del OST es esperar que el cristal oscilador esté en funcionamiento estable. Este temporizador es activado solo si se configura un oscilador en modo XT, LP o HS. No hay bits específicos de configuración para deshabilitar este timer, ni tampoco puede ser accedido por software.

###Watchdog Timer (WDT)

El watchdog timer es un temporizador de tipo RC interno (baja precisión) que se incrementa independiente del oscilador externo colocado, por lo que el WDT sigue en funcionamiento aún en ausencia de una señal de clock. Durante la operación normal, un desbordamiento de este timer provoca un RESET del microcontrolador. Puede deshabilitarse totalmente mediante un bit de configuración cuando se programa el PIC. 
Con el WDT habilitado, el programador debe evitar que el microcontrolador entre en RESET incluyendo en el programa instrucciones de reset, a intervalos regulares, y antes que se produzca el desbordamiento. Esto se realiza mediante la instrucción **clrwdt**, que lo vuelve a cero.

Si bien el estado de este timer puede controlarse por software, solo se nos permite borrarlo, o sea, que no es posible asignar ni conocer su valor.

El período del este timer (tiempo que tarda en desbordar) es de 18ms para el PIC16F628, se puede agregar un preescaler desde el registro OPTION_REG hasta una relación de 1:128, para un período de hasta 2,304 segundos. 

Se recomienda que la instrucción **clrwdt** esté en un bucle principal del programa y no dentro de interrupciones de timer, esto es para proteger situaciones donde las interrupciones funcionan pero el programa principal no (por ejemplo porque intenta ejecutar código en un lugar de memoria donde no hay, o se queda tildado en un bucle infinito), en ese caso queremos que se produzca el reset. El WDT no debe usarse para compensar errores de programación sino exclusivamente para salvar de alguna situación provocada por situaciones no contempladas, como fallas de alimentación o problemas de hardware. Es posible saber luego de un reset si ocurrió por WDT, lo cual es útil por ejemplo para avisar que algo anduvo mal.

###TMR0 Timer

Este timer se compone de un registro de 8 bits TMR0 que se encuentra en la dirección 0x01 del mapa de memoria. Tiene dos modos de operación, como timer y como contador, se configuran a través del bit **T0CS** en el registro OPTION_REG.

- T0CS==0 es modo "timer", el timer se incrementa por cada ciclo de instrucción (1:1) si no se configura preescaler, pero se le puede asignar una relación de prescaler de hasta 1:256, incrementando 1 cada 256 ciclos de instrucción. La lectura del registro TMR0 refleja el valor del timer en cualquier momento, y una escritura va a forzar el valor del timer y continuará a partir de ese valor escrito (Un detalle, luego de escribir el registro TMR0, el incremento del timer se inhibe por los siguientes dos ciclos de instrucción)

- T0CS==1 es modo "contador", el registro TMR0 se incrementará en cada flanco positivo o negativo (configurable) de la señal en el pin RA4/TCKI. El tipo de transición (positiva o negativa) necesaria para producir un incremento se configura en el bit T0SE del registro OPTION_REG, un 0 en este bit provoca incrementos en cada flanco positivo y viceversa. La frecuencia máxima permisible en el pin RA4/TCKI depende de la frecuencia de clock del micro, y si se usa preescaler o no. 

Siendo TMR0 de 8 bits, la cuenta máxima que puede adquirir es 0xFF, de 0 a 255 cuentas. Si el valor de este registro desborda, pasa de 0xFF a 0x00, automáticamente se setea el bit T0IF del registro INTCON y produce una interrupción (si está habilitada en forma particular y global).

**Preescaler**

El preescaler es un contador interno de 8 bits que divide la frecuencia. Tanto el timer TMR0 como el Watchdog comparten el mismo preescaler, si se asigna el uso de prescaler a TMR0, el Watchdog funcionará sin preescaler y viceversa. 

Cuando el preescaler es asignado al timer TMR0, se intercala este contador entre la fuente de pulsos y el conteo en el registro TMR0. Dependiendo de la relación asignada, se establece la cantidad de pulsos que debe generar la fuente, para incrementar en 1 el registro TMR0. 

Para configurarlo deben escribirse los primeros 3 bits del registro OPTION_REG, permite 8 opciones:

    bits OPTION         000   001   010   011   100    101    110    111
    para TMR0           1:2   1:4   1:8   1:16  1:32   1:64   1:128  1:256
    para WDT            1:1   1:2   1:4   1:8   1:16   1:32   1:64   1:128

Si por ejemplo asigno al TMR0 el modo de "timer" con una relación de 1:32, significa que cada 32 ciclos de instrucción el registro del Timer se incrementará en 1.

Una forma conveniente de utilizar el timer es habilitando una interrupción para que "avise" cuando se desborda, otra manera es chequear la bandera continuamente (polling), aunque el timer puede usarse así, sin interrupciones, no es la mejor manera, aquí un código para probar la versión polling:


    ;Asignación de nombres a las direcciones de memoria que vamos a usar
    
    STATUS equ 0x03 ; Registro STATUS, entre otras cosas, para seleccionar bancos
    TRISB equ 0x86  ; Registro TRISB, para configurar entradas o salidas del PUERTOB
    PORTB equ 0x06  ; Registro PORTB, bits que corresponden pines/hardware del micro
    OPTREG equ 0x01 ; Registro OPT, entre otras cosas, para configurar el timer0
    INTCON equ 0x0B ; Registro INTCON entre otras, bits de condición de interrupción
    
            org 0x00               ;indica posición 0 (reset) del microcontrolador
    
	    ;CONFIGURACIONES
	    bsf STATUS,5           ;Direccionamiento: selección BANK1
	    bcf TRISB,0            ;bit 0 del TRISB en 1 => bit 0 del PORTB como salida
	    bcf OPTREG,5           ;configuramos freq. cristal/4 como clock del TMR0
	    bcf OPTREG,3           ;Asignamos el prescaler al timer
	    bsf OPTREG,0           ;prescaler 1:256 bit [0,1,2] del OPTREG en 1.
	    bsf OPTREG,1           ;opción 111 del manual
	    bsf OPTREG,2           ;para que cuente uno cada 256 ciclos
	    bcf STATUS,5           ;Direccionamiento: selección BANK0
	    
            ;PROGRAMA PRINCIPAL
    
    bucle:      btfss INTCON,2     ;test de bandera T0IF, indica desbordó timer(overflow)
                goto bucle         ;si no esta en uno seguimos chequeando (vuelve a test)
                bcf INTCON,2       ;si está en uno, la bajamos y continúa la ejecución
    
    cambia:     btfss PORTB, 0     ;conmuta leds (si estaba encendido, apaga, y viceversa)
                goto prende
                goto apaga
    
    prende:     bsf PORTB, 0       ;pone en uno bit 0 del PORTB (enciende led)
                goto bucle
    
    apaga:      bcf PORTB, 0       ;pone en cero bit 0 del PORTB (apaga led)
                goto bucle
    
    end
    
    
