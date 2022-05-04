## Proyecto Organización de Computadores

Nicole Caicedo

Pamela Pupiales

Juan Antonio Romero


### Calculadora con microcontrolador 
La propuesta de este proyecto es crear una calculadora la cual pueda sumar, restar, multiplicar y dividir. Se realizará un circuito en cual usará un microcontrolador que será programado para poder realizar estas operaciones. 

![image](https://user-images.githubusercontent.com/89537755/166595977-9d7193aa-33e8-4e22-a134-4c1fc9b08316.png)

#### Qué es un microcontrolador
hghg
#### Qué microntrolador se va a usar
ghgh
#### Uso de clock y capacitores 

#### Caracteristicas
hghgh

#### Lenguaje que se usa 
Los microcontroladores más modernos usan el lenguaje de C. 
El lenguaje C es un lenguaje de programación muy importante en la actualidad ya que aplicaciones y sistemas operativos han sido desarrolados con este. También es la base de otros lenguajes actuales como lo son C++ , C# Y Java. 
Este lenguaje no depende del hardware. Se lo puede usar para programar sistemas operativos, hojas de cálculo e inclusive juegos. 
![image](https://user-images.githubusercontent.com/89537755/166604605-9b5d81d3-a718-4aca-a586-55dc8bdd1ce3.png)

#### Programas que se usan 
Los programas que usamos son:

-AVR O USBASP ---> es un dispositivo que sirve para cargar el programa de la compu al microcontrolador
![image](https://user-images.githubusercontent.com/89537755/166607983-308339d4-eef5-41b5-a656-5462a2c35bfa.png)

-ZADIG---> programa para que windows detecte USBASP. Windows debe autorizar y dejar cargar. Instalador de controladores

![image](https://user-images.githubusercontent.com/89537755/166608559-28d4c641-e594-4b05-a2e7-2bc1dba6339b.png)

-PROGISP   ---> Detecta cualdo el AVR está conectado al microcontrolador y en ese programa es dode se carga el código. Un archivo .hex

![image](https://user-images.githubusercontent.com/89537755/166608684-ab61a7bc-b5e0-4d73-85c6-38604d5bed52.png)

-Ambiente: microchip studio  

![image](https://user-images.githubusercontent.com/89537755/166606030-a7e7d91f-7f59-4693-abea-3c827f4ee055.png)

#### Componentes circuito de la calculadora

![image](https://user-images.githubusercontent.com/89537755/166610753-e53d4420-81ac-439d-91f6-60a3edefb642.png)

#### Codigo para la calculadora
LCD
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

### Circuto completo
#### Funcionamiento del circuito en el simulador
#### Funcionamiento del circuito fisico

