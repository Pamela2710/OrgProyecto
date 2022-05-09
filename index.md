## Proyecto Organización de Computadores

Nicole Caicedo

Pamela Pupiales

Juan Antonio Romero


### Calculadora con microcontrolador 
La propuesta de este proyecto es crear una calculadora la cual pueda sumar, restar, multiplicar y dividir. Se realizará un circuito en cual usará un microcontrolador que será programado para poder realizar estas operaciones. 

![image](https://user-images.githubusercontent.com/89537755/166595977-9d7193aa-33e8-4e22-a134-4c1fc9b08316.png)

#### Qué es un microcontrolador
Un microcontrolador es un circuito que esta integrado y nos permitira ejecutar tareas de control.Es como un computador que nos dejara hacer trabajos mediante entrada y salida.Este tiene un processador y tambien tiene la capacidad de guardar memoria. Son muy similares, solo que el atmega164a es mas actual que el otro

#### Qué microntrolador se va a usar
Vamos a utilizar el microcontrolador atmega16 para el simulador y atmega16A, este nos deja processar datos de 8 bits. Su arquitectura se basa en la RISC que tiene 131 instrucciones y tiene 32 registros integradoos, rendimiento de hasta 20 MIPS a 20 MHz

#### Uso de clock y capacitores 
Capacitor: Lo utilizamos para almacenar y entregar electricidad de manera controlada
Clock:Es la señal en binario que se usa para sincoronizar las acciones de el circuito

#### Caracteristicas
**ATMEGA164A**

![image](https://user-images.githubusercontent.com/89537755/166618115-45bcf522-8229-4b4c-9e7e-9a156e1f2598.png)

**ATMEGA16**

![image](https://user-images.githubusercontent.com/89537755/166618166-c0ca7c8b-d222-48c9-8306-3d0ca2db0ccb.png)



#### Lenguaje que se usa 
Los microcontroladores más modernos usan el lenguaje de C. 
El lenguaje C es un lenguaje de programación muy importante en la actualidad ya que aplicaciones y sistemas operativos han sido desarrolados con este. También es la base de otros lenguajes actuales como lo son C++ , C# Y Java. 
Este lenguaje no depende del hardware. Se lo puede usar para programar sistemas operativos, hojas de cálculo e inclusive juegos. 
![image](https://user-images.githubusercontent.com/89537755/166604605-9b5d81d3-a718-4aca-a586-55dc8bdd1ce3.png)

#### Programas que se usan 
Los programas que usamos son:

-AVR O USBASP ---> es un dispositivo que sirve para cargar el programa de la compu al microcontrolador
![image](https://user-images.githubusercontent.com/89537755/166607983-308339d4-eef5-41b5-a656-5462a2c35bfa.png)

![image](https://user-images.githubusercontent.com/89537755/166621306-ee240c40-2563-4138-92d6-d552d2026018.png)


-ZADIG---> programa para que windows detecte USBASP. Windows debe autorizar y dejar cargar. Instalador de controladores

![image](https://user-images.githubusercontent.com/89537755/166608559-28d4c641-e594-4b05-a2e7-2bc1dba6339b.png)

-PROGISP   ---> Detecta cualdo el AVR está conectado al microcontrolador y en ese programa es dode se carga el código. Un archivo .hex

![image](https://user-images.githubusercontent.com/89537755/166608684-ab61a7bc-b5e0-4d73-85c6-38604d5bed52.png)

-Ambiente: microchip studio  

![image](https://user-images.githubusercontent.com/89537755/166606030-a7e7d91f-7f59-4693-abea-3c827f4ee055.png)

#### Componentes circuito de la calculadora

![image](https://user-images.githubusercontent.com/89537755/166621057-cba67523-6fb7-40b7-a979-3e440ef302b4.png)


## Codigo para la calculadora
### LCD_ML.h
```markdown
/*
 * LCD_ML.h
 *
 * Created: 22-Abr-22 5:34:42 PM
 *  Author: pamel
 */ 

#include "LCD_HW.h"

/*envia comandos*/

void cmd(char cmd_data);

void cmd(char cmd_data)
{
	mode_select(0);			//selecciona el modo del comando
	
	port(cmd_data);			//envia el comando al puerto (DATABUS)
	
	enb_tri();				//ENABLE TRIGGER
}

/*INTIALLIZE LCD*/

void lcd_init(void);

void lcd_init(void)
{
	set_port();		//set del DATABUS
	
	cmd(0);			//retorna el cursor a la posicion inicial - COMMAND 0X02
	cmd(2);
	
	cmd(2);			//despliega en 2 lineas y 4 bits- COMMAND 0X28
	cmd(8);
	
	cmd(0);			//no despliega cursor ni parpadeo - COMMAND 0X0C
	cmd(12);
	
	cmd(0);			//direccion ram sin offset - COMMAND 0X06
	cmd(6);
}

/*DISPLAY CHARACHTER*/

void dis_char(char data);

void dis_char(char data)
{
	char msb, lsb;
	
	msb = data & 0xF0;		//asigna los bits mas importantes de los datos a msb
	
	lsb = data & 0x0F;		//asigna los bits menos significativos de los datos a lsb
	
	mode_select(1);			//selecciona el dta mode
	
	port(msb>>4);			//envia msb a databus
	
	enb_tri();
	
	port(lsb);				//envia lsb a DATABUS
	
	enb_tri();
}

/*SET CURSOR POSITION*/

void dis_position(char i, char j);

void dis_position(char i, char j)
{
	if (i==0)			//IF i = 0 despliega la primera linea
	{					//comando para primera linea 0X80
		cmd(8);			//envia comando msb PRIMERO y lsb SEGUNDO
		cmd(j);			//j es la posicion horizontal
	} 
	else if (i==1)		//IF i = 1 despliega en la segunda linea
	{
		cmd(12);		//comando para segunda linea es 0XC0, C = 12
		cmd(j);	
	}
}

/*CLEAR LCD*/

void clr_dis(void);

void clr_dis(void)
{
	cmd(0);		//vaciar el display 0X01
	cmd(1);
	
	cmd(0);		//retornar el cursor a inicio 0X02
	cmd(2);
}

/*DISPLAY STRING*/

void dis_string(unsigned char row, unsigned char col, char *str);

void dis_string(unsigned char row, unsigned char col, char *str)
{
	dis_position( row, col );
	
	while (*str!='\0')		//while el puntero del string no apunte a nada
	{
		dis_char(*str);		//lllama a la funcion  dis_char para desplegrarlo que el string pointer apunta
		
		++str;				//incrementa el string pointer para que apunte al siguiente character
	}
}

/*DISPLAY SHIFTTING*/

void dis_shift(char dir);

void dis_shift(char dir)
{
	if ( dir == 'R' )
	{
		cmd(1);				//comando para cambiar a la derecha 0X18
		cmd(8);
	}
	else if ( dir == 'L' )
	{
		cmd(1);				//comando para cambiar a la izquierda 0X1C
		cmd(12);
	}
}
```
### LCD_HW.h
```markdown
/*
 * LCD_HW.h
 *
 * Created: 23-Abr-22 1:11:32 PM
 *  Author: pamel
 */ 


#ifndef F_CPU
#define F_CPU 16000000UL // 16 MHz clock speed
#endif

#include <avr/io.h>

#include <util/delay.h>

#define set_bit(reg,pos) reg|=(1<<pos)		// se usa para hacer set al bit x en el registro x to 1

#define clr_bit(reg,pos) reg&=~(1<<pos)		// se usa para vacial el bit x en el registro x to 0

#define tog_bit(reg,pos) reg^=(1<<pos)		// se usa para anclar el  bit x en el registro x from x to x`

/********************************************************************************************************/

/*envia datos desde el puerto hw*/

void port(char cmd_port);

void port(char cmd_port)
{
	if(cmd_port&1)
	set_bit(PORTC,0);		//IF 1ST BIT = 1 RAISE PB0
	else
	clr_bit(PORTC,0);		//IF 1ST BIT = 0 CLR PB0
	
	if(cmd_port&2)
	set_bit(PORTC,1);		//IF 2ND BIT = 1 RAISE PB1
	else
	clr_bit(PORTC,1);		//IF 2ND BIT = 0 CLR PB1
	
	if(cmd_port&4)
	set_bit(PORTC,2);		//IF 3RD BIT = 1 RAISE PB2
	else
	clr_bit(PORTC,2);		//IF 3RD BIT = 0 CLR PB2
	
	if(cmd_port&8)
	set_bit(PORTC,3);		//IF 4TH BIT = 1 RAISE PB3
	else
	clr_bit(PORTC,3);		//IF 4TH BIT = 0 CLR PB3
}

/*cambia entre COMMAND MODE O DATA MODE*/

void mode_select(char m);

void mode_select(char m)	 
{
	if ( m == 0)
	clr_bit(PORTC,5);		// RS = 0 --> cambia a LCD CONTROL REGISTER - COMMAND MODE
	
	if ( m == 1)
	set_bit(PORTC,5);		// RS = 1 --> cambia a LCD DATA REGISTER - DATA MODE
}

/*TRIIGERS THE ENABLE BIT*/

void enb_tri(void);

void enb_tri(void)
{
	set_bit(PORTC,4);		//levanta enable en  EN = 1
	_delay_us(50);
	
	clr_bit(PORTC,4);		//ancla el enable
	_delay_us(500);			//hace un delay alto para prevenir data overflow
}

/*inicializa DATABUS a el puerto HW*/

void set_port(void);

void set_port(void){
	DDRC |= 0X3F;	//Asigna DATABUS PB0-PB3, CONTROLBUS EN-PB4 RS-PB5
}
```
### keypad.h
```markdown
/*
 * keypad_lib.h
 *
 * Created: 22-Abr-22 12:23:45 PM
 *  Author: pamel
 */ 

#ifndef F_CPU

#define F_CPU 16000000UL

#endif

#include <avr/io.h>

#define clr_bit(reg,pos) reg&=~(1<<pos)		//vaciar bit x en el registro x to 0


void key_init(void);						//inicializa el keypad

void Key_init(void)
{
	DDRA = 0x0F;			//asigna las finas de bits PA0-PA3 como outputs and columnas de bits PA4-PA7 como inputs
}

unsigned char key_scan(void);				//scanea la tecla aplastada y la devuelve 

unsigned char key_scan(void)
{
	unsigned char cnt, key;
	
	
	
	for ( cnt = 0 ; cnt < 4 ; ++cnt )
	{
		PORTA = 0XFF;				//chequea las filas una por una en primer nivel
		
		if ( cnt == 0 )
			clr_bit(PORTA,0);		//check d eprimera fila
			
		if ( cnt == 1 )
			clr_bit(PORTA,1);		//check de segunda fila
		
		if ( cnt == 2 )
			clr_bit(PORTA,2);		//check de tercera fila
		
		if ( cnt == 3 )
			clr_bit(PORTA,3);		//check de cuarta fila
		
		key = PINA & 0xF0;			//checkea si alguna tecla esta siendo aplastada
		
		if ( key != 0xF0 )			//si alguna techa esta siendo aplastada, revisa el segundo nivel
		{
			while ( ( PINA & 0xF0 ) != 0xF0 );
			break;
		}
	}
	
	if ( cnt == 4 )					//termina el scan loop del primer nivel
		return 0;
		
	switch ( cnt )					//scanea las columns
	{
		case 0:						//case 0 para primera fila
			
			switch ( key )			
				{
					case 0xE0:		//Checkea si la fila 1 col 1 --> ( 7 )
					return 0x07;
					break;
					
					case 0xD0:		//Checkea si la fila 1 col 2 --> ( 8 )
					return 0x08;
					break;
					
					case 0xB0:		//Checkea si la fila 1 col 3 --> ( 9 )
					return 0x09;
					break;
					
					case 0x70:		//Checkea si la fila 1 col 4 --> ( A )
					return 0x0A;
					break;
				}
		break;
		
		case 1:						//case 1 para segunda fila
		
			switch ( key )
				{
					case 0xE0:		//Checkea si la fila 2 col 1 --> ( 4 )
					return 0x04;
					break;
			
					case 0xD0:		//Checkea si la fila 2 col 2 --> ( 5 )
					return 0x05;
					break;
			
					case 0xB0:		//Checkea si la fila 2 col 3 --> ( 6 )
					return 0x06;
					break;
			
					case 0x70:		//Checkea si la fila 2 col 4 --> ( B )
					return 0x0B;
					break;
				}
		break;
		
		case 2:						//case 2 para tercera fila
		
			switch ( key )
				{
					case 0xE0:		//Checkea si la fila 3 col 1 --> ( 1 )
					return 0x01;
					break;
			
					case 0xD0:		//Checkea si la fila 3 col 2 --> ( 2 )
					return 0x02;
					break;
			
					case 0xB0:		//Checkea si la fila 3 col 3 --> ( 3 )
					return 0x03;
					break;
			
					case 0x70:		//Checkea si la fila 3 col 4 --> ( C )
					return 0x0C;
					break;
				}
		break;
		
		case 3:						//case 3 para cuarta fila
		
			switch ( key )
			{
					case 0xE0:		//Checkea si la fila 4 col 1 --> ( F )
					return 0x0F;
					break;
					
					case 0xD0:		//Checkea si la fila 4 col 2 --> ( 0 )
					return '0';
					break;
					
					case 0xB0:		//Checkea si la fila 4 col 3 --> ( E )
					return 0x0E;
					break;
					
					case 0x70:		//Checkea si la fila 4 col 4 --> ( D )
					return 0x0D;
					break;
			}
		break;
	}
	
	return 0;
}

```

calc.h
```markdown
/*
 * calc.h
 *
 * Created: 22-Abr-22 2:15:30 PM
 *  Author: pamel
 */ 

#include <stdio.h>

#include "keypad.h"

#include "LCD_ML.h"



static unsigned int key;		//la variable a guardar es la tecla presionada

static unsigned int op;			//op variable para guardar el operation ID y op_char para operaciones de symbolo

static char op_char;
		
long int a, b;					//a & b para guardar dos numeros de operacion
			
static double result;			//el resultado se debe guardar en esta variable 
			
static char lcd_buf[16];		//lcd_buf array es el lcd buffer



/*initialize keypad and lcd*/
void cal_init(void);

void cal_init(void)
{		
	Key_init();
	
	lcd_init();
}

/*determine calculator operation*/
void cal_op(void);

void cal_op(void)
{
	 if(key==0x0D)			//suma
	{
		key=0;
		op=1;
		op_char='+';
	}
	
	else if(key==0x0C)		//resta
	{
		key=0;
		op=2;
		op_char='-';
	}
	
	else if(key==0x0B)		//multiplicacion
	{
		key=0;
		op=3;
		op_char='x';
	}

	else if(key==0x0A)		//division
	{
		key=0;
		op=4;
		op_char='/';
	}
	
	else if(key==0x0E)		//ejecusion
	{
		key=0;
		op=5;
	}

	else if(key=='0')		//permite ZERO-Bug Fix
	{
		key=0;
	}
	
	else if(key==0x0F)		// on/clear
	{
		a=0;	
		b=0;
		result=0;
		op=0;
		key = 0;
		clr_dis();
	}
}

/*proceso de calculo*/
void cal_run(void);

void cal_run(void)
{

		if(op==0)										//si no hay operacion seleccionada guarda el primer numero
		{
			a=a*10+key;									//guarda numero de n digitos
			sprintf(lcd_buf,"%1li",a);
			dis_string(0,0,lcd_buf);
		}
														//si se selecciona alguna operacion, comienza a guardar el segundo numero
		else if(op==1 || op==2 || op==3 || op==4)
		{
			b=b*10+key;									//despliega los dos numeros al mismo tiempo en el LCD buffer
			sprintf(lcd_buf,"%1li%c%1li",a,op_char,b);
			dis_string(0,0,lcd_buf);
		}
		
		else if(op==5)									//si la operacion execute se selecciona
		{		
														//selecciona entre diferentes operaciones
			if(op_char=='+')
				result=a+b;
			
			else if(op_char=='-')
				result=a-b;
			
			else if(op_char=='x')
				result=a*b;
			
			else if(op_char=='/')
				result=(float)a/b;
														//displiega el resultado
			sprintf(lcd_buf,"%.0f",result);
			dis_string(1,12,lcd_buf);
				
			a=0;										//vacia la memoria para el siguiente calculo
			b=0;
			result=0;
			op=0;
		}
}

/*corre la calculadora*/
void calculate(void);

void calculate(void)
{
		key = key_scan();		//obtiene la tecla presionada
				
		if (key != 0)			//if key = true
		{
			cal_op();			//primero chequea si la tecla presionada es un ID de operacion
			cal_run();			//guarda el numero o aplica la operacion
			key = 0;			//limpia key para el siguiente intento
		}
}
```

cal.c
```markdown
/*
 * calc.c
 *
 * Created: 22-Abr-22 2:47:42 PM
 *  Author: pamel
 */ 

#include "calc.h"

int main(void)
{
		cal_init();
			
			_delay_ms(50);
			
			for (int start = 0;start < 25; ++start)
			{
				_delay_ms(5);
				dis_shift('L');
			}
		
    	while(1)
    	{
			calculate();
    	}
}
```

#### Funcionamiento del circuito en el simulador

![image](https://user-images.githubusercontent.com/89537755/166695578-ffc3b979-865a-4973-907b-63573f5d64b0.png)

### Circuto completo

![image](https://user-images.githubusercontent.com/89537755/166617527-d204b969-0831-4d31-bc9d-c474f12aff43.png)

#### Funcionamiento del circuito fisico

https://youtu.be/236jLhVPW8Q



