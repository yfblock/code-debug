**2023.1.1\~2023.3.1**

此阶段目标：学习rcore操作系统

下面是遇到的一些问题，以聊天记录的形式呈现

向驹韬：学长，我想请教你几个问题

![image-20230814224042999](image\image-20230814224042999.png)

向驹韬：这一段代码的功能是按地址分别分配text rodata data bss段

![image-20230814224110164](image\image-20230814224110164.png)

向驹韬：这一小段中，第二行的\*(.text.entry)我知道是载入entry.asm代码，可是下一行的\*(.text .text.\*)是什么意思呢？

向驹韬：还有就是 为什么这text段不像后面三段一样，加上\*(.stext .stext.\*)呢？

向驹韬：以上是第一个问题 谢谢学长啦

向驹韬：![image-20230814224131871](image\image-20230814224131871.png)

向驹韬：

这里是不是相当于是

for(a=sbss;a\<ebss;a++)

{

bss段\[a\]=0;

}

向驹韬：还有就是 这里的mut u8中的u8是不是代表一个字节？然后实现逐字节清零？

向驹韬：以上是第二个问题

向驹韬：![image-20230814224145315](image\image-20230814224145315.png)

向驹韬：这里的目的是要包装print函数，其中console_putchar功能是输出一个字符。

请问一下里面的mut self和fmt::Result是什么意思呢？

向驹韬：麻烦学长有空看一看，多谢多谢

陈志扬：第一个问题：\*在这里用作通配符. \*(.text .text.\*)的意思是：匹配所有文件（第一个\*）中，名字是 .text 以及类似.text.aaaaaaaaaa、.text.bbbbbbbbbb的段；至于为什么没有.stext我也不太懂，可能 object file (.o文件) 里没有这个段？

第二个问题：是的，就是这个意思. 在这个特殊的情况下，rust显得比C繁琐很多.

第三个问题：self是指向结构体的指针，想想python面向对象里面也有类似的用法；Result是用于错误处理的，比如，某函数计算某个数值，如果成功了，就返回Ok(数值)，如果失败了，就返回Err(错误信息). 在你这个例子中，没有错误处理（即返回Err）相关的语句，如果成功了，就返回Ok(一个空元组). Result是利用rust的泛型实现的，看看这个<https://skyao.io/learning-rust/std/result/result.html>

向驹韬：好的 谢谢学长！

向驹韬：

![image-20230814224207165](image\image-20230814224207165.png)

向驹韬：学长，你看这段话我这样理解对吗：trap上下文就是寄存器集合，里面存了应用程序的地址等信息。先把这些信息保存进内核栈，然后trap会对寄存器进行修改，等trap处理完之后，就从内核栈里把寄存器们复原，从而让应用程序接着上次的地址运行。

向驹韬：这段话来自文档第二章的"实现特权级的切换"

陈志扬：对的 就是这样

陈志扬：那个_alltraps是汇编写的，就是干这些事

向驹韬：

![image-20230814224220270](image\image-20230814224220270.png)

向驹韬：这段代码是用来加载多道程序的

向驹韬：18行之前我能看懂 就是对app_num进行修改，然后给每个程序分配一块空间并且初始化

向驹韬：但是我不知道18行之后，他是怎么做到把应用程序加载到空间里的

向驹韬：我尤其不太懂23行，app_start不是一个用来修改app_num的函数吗？咋还有数组和减法

陈志扬：app_start是数组啊，你看第七行，from_raw_parts返回的是数组

陈志扬："from_raw_parts返回的是数组"，这个说的不太准确，我的理解是，from_raw_parts 是把实际存在的数组转换为语义上的数组

向驹韬：那这个数组里存的是什么啊，还是说只是单纯的创建了一个数组，然后在创建的时候调用了add函数并让numapp+1吗

陈志扬：那个数组在这，第二章里面有写

向驹韬：哦哦哦我明白了 后面那两个是from raw parts的两个参数，形成了一个切片，里面有应用程序的地址，是这样吗

陈志扬：对

向驹韬：

![image-20230814224231835](image\image-20230814224231835.png)

向驹韬：请问一下这段代码的作用是把usize类型的物理地址和物理页号转化为真正的物理地址和物理页号吗？

陈志扬：真正的物理地址和物理页号也是usize类型的呀，只是做了取前几位的处理而已

向驹韬：换句话说，就是用usize类型的话，内核不能识别，需要这个函数来转化一下，从而变得可识别吗

陈志扬：对的

向驹韬：另外就是，这里的v & ( (1 \<\< PA_WIDTH_SV39) - 1 )我不明白是什么意思

陈志扬：这个的作用其实就是取后56位

1 \<\< PA_WIDTH_SV39 == 1000000\.....(共56个0)

(1 \<\< PA_WIDTH_SV39 -1 ) == 011111111\....（共56个1）

v 和 ( (1 \<\< PA_WIDTH_SV39) - 1 ) 按位与，就可以取前56位了

向驹韬：好的谢谢
