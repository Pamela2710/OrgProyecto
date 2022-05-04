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


#### Codigo para la calculadora
LCD_ML.h
```markdown
/*
 * LCD_ML.h
 *
 * Created: 22-Abr-22 5:34:42 PM
 *  Author: pamel
 */ 

#include "LCD_HW.h"

/*SEND COMMANDS OVER DATA BUS*/

void cmd(char cmd_data);

void cmd(char cmd_data)
{
	mode_select(0);			//SELECT COMMAND MODE
	
	port(cmd_data);			//SEND COMAND TO PORT (DATABUS)
	
	enb_tri();				//ENABLE TRIGGER
}

/*INTIALLIZE LCD*/

void lcd_init(void);

void lcd_init(void)
{
	set_port();		//SET DATABUS
	
	cmd(0);			//RETURN CURSOR TO HOME POISTION - COMMAND 0X02
	cmd(2);
	
	cmd(2);			//DISPLAY IN 2-LINES AND 4-BIT DATA MODE - COMMAND 0X28
	cmd(8);
	
	cmd(0);			//DISPLAY NO CURSOR AND NO BLINK - COMMAND 0X0C
	cmd(12);
	
	cmd(0);			//DD RAM ADDRESS WITH NO OFFSET - COMMAND 0X06
	cmd(6);
}

/*DISPLAY CHARACHTER*/

void dis_char(char data);

void dis_char(char data)
{
	char msb, lsb;
	
	msb = data & 0xF0;		//ASSIGN DATA MOST SIGNIFICANT BITS TO msb
	
	lsb = data & 0x0F;		//ASSIGN DATA LEAST SIGNIFICANT BITS TO lsb
	
	mode_select(1);			//SELECT DATA MODE
	
	port(msb>>4);			//SEND msb OVER DATABUS
	
	enb_tri();
	
	port(lsb);				//SEND lsb OVER DATABUS
	
	enb_tri();
}

/*SET CURSOR POSITION*/

void dis_position(char i, char j);

void dis_position(char i, char j)
{
	if (i==0)			//IF i = 0 DISPLAY IN FIRST LINE
	{					//COMMAND FOR FIRST LINE IS 0X80
		cmd(8);			//SEND COMMAND msb FIRST AND lsb SECOND
		cmd(j);			//j IS THE HORIZONTAL POSITION
	} 
	else if (i==1)		//IF i = 1 DISPLAY IN SECOND LINE
	{
		cmd(12);		//COMMAND FOR SECOND LINE IS 0XC0, C = 12
		cmd(j);	
	}
}

/*CLEAR LCD*/

void clr_dis(void);

void clr_dis(void)
{
	cmd(0);		//CLEAR DISPLAY COMMAND 0X01
	cmd(1);
	
	cmd(0);		//RETTURN CURSOR HOME COMMAND 0X02
	cmd(2);
}

/*DISPLAY STRING*/

void dis_string(unsigned char row, unsigned char col, char *str);

void dis_string(unsigned char row, unsigned char col, char *str)
{
	dis_position( row, col );
	
	while (*str!='\0')		//while string pointer do not point to nothing
	{
		dis_char(*str);		//call dis_char functions to display what string pointer points to
		
		++str;				//increment string pointer to point to the next character in the array
	}
}

/*DISPLAY SHIFTTING*/

void dis_shift(char dir);

void dis_shift(char dir)
{
	if ( dir == 'R' )
	{
		cmd(1);				//SHIFT RIGHT COMMAND 0X18
		cmd(8);
	}
	else if ( dir == 'L' )
	{
		cmd(1);				//SHIFT LEFT COMMAND 0X1C
		cmd(12);
	}
}
```
LCD_HW.h
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

#define set_bit(reg,pos) reg|=(1<<pos)		// used to set bit x on register x to 1

#define clr_bit(reg,pos) reg&=~(1<<pos)		// used to clear bit x on register x to 0

#define tog_bit(reg,pos) reg^=(1<<pos)		// used to toggle bit x on register x from x to x`

/********************************************************************************************************/

/*SEND DATA TO HW PORT*/

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

/*SWITCH BETWEEN COMMAND MODE OR DATA MODE*/

void mode_select(char m);

void mode_select(char m)	 
{
	if ( m == 0)
	clr_bit(PORTC,5);		// RS = 0 --> SWITCH TO LCD CONTROL REGISTER - COMMAND MODE
	
	if ( m == 1)
	set_bit(PORTC,5);		// RS = 1 --> SWITCH TO LCD DATA REGISTER - DATA MODE
}

/*TRIIGERS THE ENABLE BIT*/

void enb_tri(void);

void enb_tri(void)
{
	set_bit(PORTC,4);		//RAISE ENABLE EN = 1
	_delay_us(50);
	
	clr_bit(PORTC,4);		//TOGGLE ENABLE
	_delay_us(500);			//HIGH DELAY TO PREVENT DATA OVER FLOW
}

/*INITIALIZE DATABUS TO HW PORT*/

void set_port(void);

void set_port(void){
	DDRC |= 0X3F;	//ASSIGN DATABUS PB0-PB3, CONTROLBUS EN-PB4 RS-PB5
}
```
keypad.h
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

#define clr_bit(reg,pos) reg&=~(1<<pos)		//used to clear bit x on register x to 0


void key_init(void);						//initialize keypad

void Key_init(void)
{
	DDRA = 0x0F;			//assign rows bits PA0-PA3 as outputs and columns bits PA4-PA7 as inputs
}

unsigned char key_scan(void);				//scan pressed key and return it

unsigned char key_scan(void)
{
	unsigned char cnt, key;
	
	
	
	for ( cnt = 0 ; cnt < 4 ; ++cnt )
	{
		PORTA = 0XFF;				//first level check rows one by one
		
		if ( cnt == 0 )
			clr_bit(PORTA,0);		//first row check
			
		if ( cnt == 1 )
			clr_bit(PORTA,1);		//second row check
		
		if ( cnt == 2 )
			clr_bit(PORTA,2);		//third row check
		
		if ( cnt == 3 )
			clr_bit(PORTA,3);		//fourth row check
		
		key = PINA & 0xF0;			//check if any key is pressed
		
		if ( key != 0xF0 )			//if any key pressed go to the second level
		{
			while ( ( PINA & 0xF0 ) != 0xF0 );
			break;
		}
	}
	
	if ( cnt == 4 )					//end of first level scan loop
		return 0;
		
	switch ( cnt )					//scan columns
	{
		case 0:						//case 0 for first row
			
			switch ( key )			
				{
					case 0xE0:		//Check if row 1 col 1 --> ( 7 )
					return 0x07;
					break;
					
					case 0xD0:		//Check if row 1 col 2 --> ( 8 )
					return 0x08;
					break;
					
					case 0xB0:		//Check if row 1 col 3 --> ( 9 )
					return 0x09;
					break;
					
					case 0x70:		//Check if row 1 col 4 --> ( A )
					return 0x0A;
					break;
				}
		break;
		
		case 1:						//case 1 for second row
		
			switch ( key )
				{
					case 0xE0:		//Check if row 2 col 1 --> ( 4 )
					return 0x04;
					break;
			
					case 0xD0:		//Check if row 2 col 2 --> ( 5 )
					return 0x05;
					break;
			
					case 0xB0:		//Check if row 2 col 3 --> ( 6 )
					return 0x06;
					break;
			
					case 0x70:		//Check if row 2 col 4 --> ( B )
					return 0x0B;
					break;
				}
		break;
		
		case 2:						//case 2 for third row
		
			switch ( key )
				{
					case 0xE0:		//Check if row 3 col 1 --> ( 1 )
					return 0x01;
					break;
			
					case 0xD0:		//Check if row 3 col 2 --> ( 2 )
					return 0x02;
					break;
			
					case 0xB0:		//Check if row 3 col 3 --> ( 3 )
					return 0x03;
					break;
			
					case 0x70:		//Check if row 3 col 4 --> ( C )
					return 0x0C;
					break;
				}
		break;
		
		case 3:						//case 3 for fourth row
		
			switch ( key )
			{
					case 0xE0:		//Check if row 4 col 1 --> ( F )
					return 0x0F;
					break;
					
					case 0xD0:		//Check if row 4 col 2 --> ( 0 )
					return '0';
					break;
					
					case 0xB0:		//Check if row 4 col 3 --> ( E )
					return 0x0E;
					break;
					
					case 0x70:		//Check if row 4 col 4 --> ( D )
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



static unsigned int key;		//key variable to store pressed key

static unsigned int op;			//op variable to store operation ID and op_char for operation symbol

static char op_char;
		
long int a, b;					//a & b variables to store two numbers of operation
			
static double result;			//result value should be stored in this variable
			
static char lcd_buf[16];		//lcd_buf array is the lcd buffer



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
	 if(key==0x0D)			//operation add
	{
		key=0;
		op=1;
		op_char='+';
	}
	
	else if(key==0x0C)		//operation subtraction
	{
		key=0;
		op=2;
		op_char='-';
	}
	
	else if(key==0x0B)		//operation multiplication
	{
		key=0;
		op=3;
		op_char='x';
	}

	else if(key==0x0A)		//operation division
	{
		key=0;
		op=4;
		op_char='/';
	}
	
	else if(key==0x0E)		//operation execution
	{
		key=0;
		op=5;
	}

	else if(key=='0')		//allow ZERO-Bug Fix
	{
		key=0;
	}
	
	else if(key==0x0F)		//operation on/clear
	{
		a=0;	
		b=0;
		result=0;
		op=0;
		key = 0;
		clr_dis();
	}
}

/*run the calculation process*/
void cal_run(void);

void cal_run(void)
{

		if(op==0)										//if no operation is selected then store the first number
		{
			a=a*10+key;									//storing number of n digits
			sprintf(lcd_buf,"%1li",a);
			dis_string(0,0,lcd_buf);
		}
														//if Any operation is selected then start storing the second number
		else if(op==1 || op==2 || op==3 || op==4)
		{
			b=b*10+key;									//display both numbers at the same time through lcd buffer
			sprintf(lcd_buf,"%1li%c%1li",a,op_char,b);
			dis_string(0,0,lcd_buf);
		}
		
		else if(op==5)									//if operation execute is selected
		{		
														//select between different operations 
			if(op_char=='+')
				result=a+b;
			
			else if(op_char=='-')
				result=a-b;
			
			else if(op_char=='x')
				result=a*b;
			
			else if(op_char=='/')
				result=(float)a/b;
														//display the result 
			sprintf(lcd_buf,"%.0f",result);
			dis_string(1,12,lcd_buf);
				
			a=0;										//clear the memory for next calculation
			b=0;
			result=0;
			op=0;
		}
}

/*run the calculator*/
void calculate(void);

void calculate(void)
{
		key = key_scan();		//obtain key pressed
				
		if (key != 0)			//if key = true
		{
			cal_op();			//first check if pressed key is operation identifier
			cal_run();			//store number or apply operation
			key = 0;			//clear key for next trial
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
		
			dis_string(0,0,"Organización de");
			dis_string(1,0,"Computadores");
			
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

![image](https://user-images.githubusercontent.com/89537755/166611905-1e1fd8c6-814b-43c8-bd4c-737315ca75f5.png)

### Circuto completo

![image](https://user-images.githubusercontent.com/89537755/166617527-d204b969-0831-4d31-bc9d-c474f12aff43.png)

#### Funcionamiento del circuito fisico



