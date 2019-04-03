#Print && Sys.stdout && Sys.stdin && Raw_input

##print && sys.stdout 输出

###一般console输出
`print 'hello' 等价于 sys.stdout.write('hello'+'\n')`  

###重定向输出

```  
f = open('out.log', 'w')
sys.stdout = f
print 'hello'
```  

This "hello" can't be viewed on concole, This "hello" is in file out.log  
记住，如果你还想在控制台打印一些东西的话，最好先将原始的控制台对象引用保存下来，向文件中打印之后再恢复 sys.stdout  

```  
__console__=sys.stdout 

# redirection start # 

... 

# redirection end 

sys.stdout=__console__
```  

##raw_input && sys.stdin 输入
`data_input = raw_input('Hello: ')`  
等价于  

```  
print 'Hello: ',
data_input = sys.stdin.readline()[:-1]
```  

-1 to discard the '\n' in input stream  
