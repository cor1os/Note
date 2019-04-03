# 跳转指令总结
---

#### and or xor



#### flag

* AF 辅助进位 [auxiliary]  
* CF 进位标志 [carry]  
* DF 方向标志 [direction]  
* IF 中断标志 [interrupt]  
* OF 溢出标志 [overflow]  
* PF 奇偶标志 [Parity]  
* SF 符号标志 [Symbol]  
* **ZF 零位标志 [zero]**  

#### test cmp

`test dst,src`  
`test`中，`dst`和`src`进行逻辑`与`运算`and`  
`dst`和`src`都不保存结果，只对flag进行操作  

常用来测试寄存器是否为空eg:  
    `test ecx,ecx`  
    `jz somewhere`  
> 如果ecx为零,设置ZF零标志为1,jz跳转  

`cmp dst,src`  
`cmp`中，`dst`和`src`进行算术减法运算  
`dst`和`src`都不保存结果，只对flag进行操作  
eg:  
    `cmp eax,2`  
    `jz somewhere`  
>  如果eax-2=0即eax＝2就设置ZF为1,如果ZF为1，则跳转  

#### jc jnc jz jnz

JC   如果进位位被置位则跳转    进位标志＝1  JB，JNAE   JNC  
JNC  如果进位位没有置位则跳转  进位标志＝0  JNB，JAE    JC  
JZ   如果0标志被置位则跳转     0标志＝1     JE        JNZ  
JNZ  如果0标志没有置位则跳转   0标志＝0     JNE        JZ  
