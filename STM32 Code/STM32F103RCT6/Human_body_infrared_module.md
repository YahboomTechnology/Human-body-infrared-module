# Human infrared sensing module: control LED_GPIO input/output

## Hardware wiring

![1](..\Human_body_infrared_module\1.png)

| Human infrared sensing module | STM32F103RCT6 |
| :---------------------------: | :-----------: |
|              VCC              |    5V/3.3V    |
|              OUT              |      PA1      |
|              NC               |               |
|              GND              |      GND      |

## Brief principle

### Circuit schematic

![2](..\Human_body_infrared_module\2.png)

The LED is connected to the PB4 pin, you need to pay attention to the pin configuration of PB4 (see the code for the specific configuration):

PB4 output high level, LED on;

The PB4 output is low and the LED is off.

When the human infrared sensing module detects an obstacle:

OUT outputs a high level and the LED lights up.

When the human infrared sensor module does not detect an obstacle:

OUT outputs a low level and the LED goes off.

## Main code

### main.c

```
#include "stm32f10x.h"
#include "Human_Body_Infrared_Moudle.h"
#include "LED.h"


int main(void)
{
    LED_Init();//LED初始化(PB4)
		Human_Body_Infrared_Moudle_Init();//人体红外模块初始化(PA1)
    
    while(1)
    {
			if(GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_1) == Bit_SET)//判断人体红外模块是否检测到人
        {
            
            GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_SET);//LED 亮
        }
        else
        {
            GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_RESET);//LED 灭
        }
    }
}
```

### LED.c

```
#include "LED.h"

void LED_Init(void)//LED初始化(PB4)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    /* Enable GPIOB and AFIO clocks */
    /* 使能GPIOB和功能复用IO时钟 */
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB | RCC_APB2Periph_AFIO, ENABLE); 
    
    /* JTAG-DP Disabled and SW-DP Enabled */
    /* 禁用JTAG 启用SWD */
    GPIO_PinRemapConfig(GPIO_Remap_SWJ_JTAGDisable, ENABLE);
    
    /* Configure PB4 in output pushpull mode */
    /* 配置PB4 推挽输出模式 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;   
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; 
    GPIO_Init(GPIOB, &GPIO_InitStructure);
    
    /* Set the GPIOB port pin 4 */
    /* 设置PB4端口数据位 */
    GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_RESET);
}
```

### LED.h

```
#ifndef __LED_H__
#define __LED_H__

#include "stm32f10x.h"

void LED_Init(void);//LED初始化(PB4)

#endif
```

### Human_Body_Infrared_Moudle.c

```
#include "Human_Body_Infrared_Moudle.h"

void Human_Body_Infrared_Moudle_Init(void)//人体红外模块初始化(PA1)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    /* Enable GPIOA */
    /* 使能GPIOA时钟 */
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE); 
    
    /* Configure PA1 in Floating input mode */
    /* 配置PA1 浮空输入模式 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_1;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;   
    GPIO_Init(GPIOA, &GPIO_InitStructure);  
}
```

### Human_Body_Infrared_Moudle.h

```
#ifndef __HUMAN_BODY_INFRARED_MOUDLE_H__
#define __HUMAN_BODY_INFRARED_MOUDLE_H__

#include "stm32f10x.h"

void Human_Body_Infrared_Moudle_Init(void);//人体红外模块初始化(PA1)

#endif
```

## Phenomenon

After downloading the program, press the Reset key once, and the downloaded program will run.

When the human infrared sensing module detects an obstacle: OUT will output a high level and the LED will light up.

When the human infrared sensor module does not detect an obstacle: OUT outputs a low level and the LED goes off.