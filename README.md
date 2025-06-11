# 串口类驱动
基于AT32全系列单片机编写。
---------------------

用法
-----

添加文件夹目录下的所有文件到工程中。
```c
#include "at32_Usart.h" //包含驱动文件
#include "util_queue.h" //包含队列文件
#include "userconfig.h" //包含配置文件（可根据实际情况选择是否包含）
```

基于环形队列实现<[一篇文章为你讲透环形队列（全文代码详解）-阿里云开发者社区](https://developer.aliyun.com/article/1099944)>

使用前通过AT32_Work_Bench配置初始化串口并生成生成代码，使用时需在某处声明使用的串口，并调用初始化函数对环形队列进行初始化，再将对应的串口时基本函数进行周期性调用,以串口1为例；

```c
#include "at32_Usart.h" //包含驱动文件
#define USART1_ENABLE
//#define USART2_ENABLE
//#define USART3_ENABLE
//#define UART4_ENABLE
//#define UART4_ENABLE

uint8_t rx_buff[];
uint8_t tx_buff[];
int main(void)
{
 /**
 * Your other code
 * */
    BSP_UsartInit();
    while(1)
    {
            /**
            * Your other code
            * */
        usart_sendData(0, tx_buff,10);//串口1发送数据，长度10字节
        usart_receiveData(0,rx_buff); //串口1读取数据
    }
    
}

/**
  * @brief  this function handles systick handler.
  * @param  none
  * @retval none
  */
void SysTick_Handler(void)
{
    /* 1ms 周期执行一次 */
    USART3_TimeOutCounter();
}


```

对应的USART1_IRQHandler中断函数在驱动中已经集成，无需编写，但注意目前使用的是接收和发送中断，更换中断择要在USART1_IRQHandler函数中手动修改对应的标志位。

由于是基于环形队列的方式实现，所以缓冲区在收到数据时要及时处理，否则环形队列满了之后就不会在接收数据。

此外，如果包含了userconfig.h这个配置文件的话则不需要自己声明使用的串口，在userconfig.h文件里勾选对应的串口即可。


![image-20250611145203335](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250611145203335.png)

