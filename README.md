


@[TOC](文章目录)



# 一、通过CubeMX配置项目并实现LED流水灯
## 1.项目配置
1、基于芯片开始项目的创建
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/aa10d5467f5c45458b19af054d53522e.jpeg#pic_center)
2、注意输入芯片型号
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fbd98d6c344f4c129f375f1f06e9265c.jpeg#pic_center)
3、配置调试接口
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/267a40f591954161afb0c48f729cd728.jpeg#pic_center)
4、配置时钟模式，具体如下
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6c7ea48ccbed44b59e68c8c2a003f205.jpeg#pic_center)
Disable 内部时钟
BYPASS：旁路时钟
Crystal:晶振/陶瓷振荡器

5、配置GPIO
这里我们需要三个输出口，简单点，就选选用PA0,PA1,PA2,三个引脚
点击要用的引脚，然后设置，注意三个都要设置
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6ed715c9a56a4dc88b39f211d8fc2d97.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c18bc15e7df24753bd748d984465fecb.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/94f69f65b56a427bbf30e772861a71dc.jpeg#pic_center)
6、时钟配置，
具体配置如下图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/94dca138ab1c494ca1dcef5b43e29797.jpeg#pic_center)
7、工程配置
点击Project manager
自己命名工程名，不要出现空格，选择路径，最好自己新建一个文件夹
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bc352793b2634c0ab019cb2907505cf9.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3e6a957f52a74639a5f29fcdcc63e1a7.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3f40e0f6210d4e628a83d74d77746bf0.jpeg#pic_center)
到此我们就成功创建完一个工程
## 2、LED流水灯的实现
1、首先我们回到keil
找到main.c文件
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a4f63fcffbd24cb2a7a2d9bf9ab709de.jpeg#pic_center)
在main函数的while循环中写入以下代码
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7cb8d6bb1e4e4d02a78551c0d5352232.jpeg#pic_center)
然后点击build
2、接着我们烧入程序
要注意程序文件的选择，电平的设置
先点击读器件信息，然后点击开始编程

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bebdd41e78ba4066843ba0ae2689b4f6.jpeg#pic_center)
一些线路的连接可参考以下连接接，连接: link 注意自己选的引脚
效果图如下：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3cfc3dd21f0d4e4f9b40df1f7fc80abc.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/50db6e1331df4734ae7ee01e8e0dbb50.jpeg#pic_center)



# 二、中断控制实现LED流水灯
## 1、什么是中断，中断有什么作用
中断是指CPU在执行当前程序时系统出现了某种状况，使得CPU必须停止当前程序，而去执行另一段程序来处理的出现的紧急事务，处理结束后CPU再返回到原先暂停的程序继续执行，这个过程就称为中断。
使得计算机系统具备应对对处理突发事件的能力，使其能及时响应紧急事件。
提高处理器效率，如果没有中断系统，CPU就只能按照原来的程序编写的先后顺序，对各个外设进行查询和处理，即轮询工作方式，轮询方法貌似公平，但实际工作效率却很低。
这里仅做简单介绍，具体的可自行查找。

## 2、项目创建

在第一个LED流水灯的基础上，我们还需要进行以下配置
1、除以上三个引脚，我们还需要在选一个引脚，作为中断的触发这里选用PB5
具体操作如下图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a284480aa5a9400997c94f4e68e448fd.jpeg#pic_center)


![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5185dcecbee54c66a68fbb3f8b779c89.jpeg#pic_center)

2、时钟配置

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f5a8a99d520040c593b3a37da25dd691.jpeg#pic_center)



3、NVIC配置如下图:
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9a532c9a84ca42b8b6b9f84e9fa5b67c.jpeg#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5a8bcdde224f4a0ea5c952b16df9cfee.jpeg#pic_center)

然后就行生成代码步骤即可


## 3、代码实现
1、在main函数上写入

```c
uint32_t sign=0;//自定义中断标识符号
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin){
  if(GPIO_Pin == SWITCH_Pin){
      //获取B5的电位
     GPIO_PinState pinState = HAL_GPIO_ReadPin(SWITCH_GPIO_Port,SWITCH_Pin);
 
      //高电位
      if(pinState==GPIO_PIN_SET)//PB5高电位流水灯亮
      {
		  sign=1;
	  }
      //低电位
      else if(pinState==GPIO_PIN_RESET)//PB5低电位流水灯灭
	  {
		  sign=0;//PB5低电位标识
	  }
      } 
   }

```


##  4、线路连接及效果演示
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/95055d258ae94966b0a5bd8b3f676c63.jpeg#pic_center)
PA0----红
PA1----黄
PA2----绿

PB5做为中断的触发。
实物连接如下图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7a0d5e823ba2419f9850f03251939e42.jpeg#pic_center)


2、效果图如下

流水灯及中断

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/76f2d8737bcb430399779e67b0361dce.jpeg#pic_center)



---

# 总结

（一）对开发工具与配置流程的熟悉
通过使用 CubeMX 和 Keil 这两个开发工具协同完成项目，深入熟悉了从项目创建、各种硬件相关配置（如 GPIO、时钟、NVIC 等）到代码编写、编译以及烧录的完整嵌入式开发流程。掌握了 CubeMX 图形化界面下方便快捷地对芯片各项功能进行精准配置的方法，以及在 Keil 中进行代码完善和最终程序生成的操作要点，提高了自己利用工具进行嵌入式项目开发的熟练度。
（二）深入理解中断机制
在实验之前，虽然对中断有理论上的了解，但通过实际动手配置中断引脚、编写中断回调函数以及在主程序中依据中断标识进行相应逻辑处理，真正体会到了中断在嵌入式系统中实时响应外部事件、灵活改变程序执行流程的作用机制。明白了如何通过中断来增强系统的交互性和灵活性，使其不再局限于单一的顺序执行逻辑，为后续处理更复杂的实时交互场景打下了坚实基础。
（三）代码编写与逻辑实现能力提升
编写代码实现流水灯以及结合中断控制流水灯状态的过程中，锻炼了自己运用 HAL 库函数（如 GPIO 操作函数、中断相关函数等）来准确控制硬件的能力，同时也提升了利用变量、条件判断（if 语句）、状态切换（switch 语句）等方式构建合理逻辑实现预期功能的编程思维。在处理中断回调函数与主程序中对中断标识的判断及对应操作的交互过程中，对程序的整体架构和逻辑连贯性有了更深入的把握，进一步提高了代码的组织和实现复杂功能的能力。
（四）硬件与软件结合的实践认知
整个实验过程充分体现了硬件连接和软件编程相互配合的重要性。只有正确配置硬件引脚的功能、连接好对应的线路，同时编写准确无误的软件代码，才能实现预期的 LED 流水灯及中断控制效果。任何硬件连接上的失误或者软件代码中的逻辑错误，都会导致最终功能无法正常实现，这让我更加深刻地认识到嵌入式开发中软硬件协同的紧密关系，培养了从整体角度去排查和解决问题的思维方式。
# 参考博客
https://blog.csdn.net/ssj925319/article/details/111984002
https://blog.csdn.net/qq_43279579/article/details/112233696
https://blog.csdn.net/qq_43279579/article/details/112213196
