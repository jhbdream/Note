# 系统初始化流程

以 STM32 系列单片机为例，系统初始化的工作在 ```rtthread_startup()``` 函数中完成。

```
int $Sub$$main(void)
{
    rt_hw_interrupt_disable();
    rtthread_startup();
    return 0;
}
```

## rt_hw_board_init()

这个函数进行板级初始化，在这个函数中调用了 ``` HAL_Init() ``` 

调用 ``` SystemClock_Config() ``` 对系统时钟进行配置，此函数来自STM32cube配置生成

调用```rt_hw_systick_init()``` 根据系统节拍，配置系统滴答定时器。

如果在宏定义定义了```RT_USING_HEAP``` ,调用```    rt_system_heap_init((void *)HEAP_BEGIN, (void *)HEAP_END)``` 用来分配堆内存空间等。其中堆内存的分配地址范围由 board.h 中的宏定义指定。

调用```rt_hw_pin_init()``` 对PIN 设备驱动初始化。具体包含了开启硬件时钟，注册pin设备。

调用```rt_hw_usart_init()``` 对串口设备初始化。包括对硬件的配置初始化。注册串口设备。

调用```rt_console_set_device()``` 设置控制台输出设备。

调用```rt_components_board_init()``` 对板子组件进行初始化。根据宏定义的设备进行依次初始化。

## rt_show_version()

打印出RtThread图标和系统版本等信息。

## rt_system_timer_init()

对系统定时器进行初始化。在此处对定时器的链表初始化。

## rt_system_scheduler_init()

对内核调度相关链表资源初始化。

## rt_system_signal_init()

## rt_application_init() 

初始化线程，创建```main_thread_entry```线程。

## rt_system_timer_thread_init()

如果启用了软件定时器，在这里对软件定时器进行初始化。创建定时器线程。                           

## rt_thread_idle_init()

初始化空闲线程，并且启动空闲线程。空闲线程的优先级最低。当系统中无其他线程时，调度器调度空闲线程。

## rt_hw_spin_lock(&_cpus_lock)

支持SMP时启用

## rt_system_scheduler_start()

启动调度区，选择执行优先级最高的任务。

## 至此系统初始化完成

