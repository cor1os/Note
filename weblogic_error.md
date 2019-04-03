# weblogic error

CVE-2017-3248检测

37行

```
####<2017-9-12 ����02ʱ54��57�� CST> <Warning> <RMI> <pcsapp1> <proxyserver> <[ACTIVE] ExecuteThread: '223' for queue: 'weblogic.kernel.Default (self-tuning)'> <<WLS Kernel>> <> <> <1505199297261> <BEA-080003> <RuntimeException thrown by rmi server: weblogic.common.internal.RMIBootServiceImpl.authenticate(Lweblogic.security.acl.UserInfo;)
 java.lang.ClassCastException: $Proxy95.
java.lang.ClassCastException: $Proxy95
	at weblogic.rjvm.MsgAbbrevInputStream.readClassDescriptor(MsgAbbrevInputStream.java:404)
	at weblogic.utils.io.ChunkedObjectInputStream$NestedObjectInputStream.readClassDescriptor(ChunkedObjectInputStream.java:267)
	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1565)
	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1496)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1732)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1329)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:351)
	at weblogic.utils.io.ChunkedObjectInputStream.readObject(ChunkedObjectInputStream.java:197)
	at weblogic.rjvm.MsgAbbrevInputStream.readObject(MsgAbbrevInputStream.java:598)
	at weblogic.utils.io.ChunkedObjectInputStream.readObject(ChunkedObjectInputStream.java:193)
	at weblogic.common.internal.RMIBootServiceImpl_WLSkel.invoke(Unknown Source)
	at weblogic.rmi.internal.BasicServerRef.invoke(BasicServerRef.java:590)
	at weblogic.rmi.internal.wls.WLSExecuteRequest.run(WLSExecuteRequest.java:118)
	at weblogic.work.ExecuteThread.run(ExecuteThread.java:173)
>
####<2017-9-12 ����02ʱ54��59�� CST> <Warning> <RMI> <pcsapp1> <proxyserver> <[ACTIVE] ExecuteThread: '114' for queue: 'weblogic.kernel.Default (self-tuning)'> <<WLS Kernel>> <> <> <1505199299258> <BEA-080003> <RuntimeException thrown by rmi server: weblogic.common.internal.RMIBootServiceImpl.authenticate(Lweblogic.security.acl.UserInfo;)
 java.lang.ClassCastException: $Proxy95.
java.lang.ClassCastException: $Proxy95
	at weblogic.rjvm.MsgAbbrevInputStream.readClassDescriptor(MsgAbbrevInputStream.java:404)
	at weblogic.utils.io.ChunkedObjectInputStream$NestedObjectInputStream.readClassDescriptor(ChunkedObjectInputStream.java:267)
	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1565)
	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1496)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1732)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1329)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:351)
	at weblogic.utils.io.ChunkedObjectInputStream.readObject(ChunkedObjectInputStream.java:197)
	at weblogic.rjvm.MsgAbbrevInputStream.readObject(MsgAbbrevInputStream.java:598)
	at weblogic.utils.io.ChunkedObjectInputStream.readObject(ChunkedObjectInputStream.java:193)
	at weblogic.common.internal.RMIBootServiceImpl_WLSkel.invoke(Unknown Source)
	at weblogic.rmi.internal.BasicServerRef.invoke(BasicServerRef.java:590)
	at weblogic.rmi.internal.wls.WLSExecuteRequest.run(WLSExecuteRequest.java:118)
	at weblogic.work.ExecuteThread.run(ExecuteThread.java:173)
>
####<2017-9-12 ����02ʱ55��32�� CST> <Warning> <Socket> <pcsapp1> <proxyserver> <ExecuteThread: '0' for queue: 'weblogic.socket.Muxer'> <<WLS Kernel>> <> <> <1505199332266> <BEA-000450> <Socket 36 internal data record unavailable (probable closure due idle timeout), event received 17>
```

反弹shell

101行

```
####<2017-9-12 ����03ʱ01��11�� CST> <Error> <RJVM> <pcsapp1> <proxyserver> <ExecuteThread: '3' for queue: 'weblogic.socket.Muxer'> <<WLS Kernel>> <> <> <1505199671325> <BEA-000503> <Incoming message header or abbreviation processing failed
 org.apache.commons.collections.FunctorException: InstantiateTransformer: Constructor threw an exception
org.apache.commons.collections.FunctorException: InstantiateTransformer: Constructor threw an exception
	at org.apache.commons.collections.functors.InstantiateTransformer.transform(InstantiateTransformer.java:114)
	at org.apache.commons.collections.functors.ChainedTransformer.transform(ChainedTransformer.java:122)
	at org.apache.commons.collections.map.LazyMap.get(LazyMap.java:157)
	at sun.reflect.annotation.AnnotationInvocationHandler.invoke(AnnotationInvocationHandler.java:50)
	at $Proxy94.entrySet(Unknown Source)
	at sun.reflect.annotation.AnnotationInvocationHandler.readObject(AnnotationInvocationHandler.java:327)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1849)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1753)
	at weblogic.rjvm.MsgAbbrevJVMConnection.readMsgAbbrevs(MsgAbbrevJVMConnection.java:227)
	at weblogic.rjvm.MsgAbbrevInputStream.init(MsgAbbrevInputStream.java:213)
	at weblogic.rjvm.t3.MuxableSocketT3.dispatch(MuxableSocketT3.java:323)
	at weblogic.socket.BaseAbstractMuxableSocket.dispatch(BaseAbstractMuxableSocket.java:297)
	at weblogic.socket.EPollSocketMuxer.dataReceived(EPollSocketMuxer.java:215)
	at weblogic.socket.EPollSocketMuxer.processSockets(EPollSocketMuxer.java:177)
	at weblogic.socket.SocketReaderRequest.run(SocketReaderRequest.java:29)
	at weblogic.socket.SocketReaderRequest.execute(SocketReaderRequest.java:43)
	at weblogic.kernel.ExecuteThread.execute(ExecuteThread.java:145)
	at weblogic.kernel.ExecuteThread.run(ExecuteThread.java:117)

Caused By: java.lang.reflect.InvocationTargetException
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:39)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:27)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:513)
	at org.apache.commons.collections.functors.InstantiateTransformer.transform(InstantiateTransformer.java:105)
	at org.apache.commons.collections.functors.ChainedTransformer.transform(ChainedTransformer.java:122)
	at org.apache.commons.collections.map.LazyMap.get(LazyMap.java:157)
	at sun.reflect.annotation.AnnotationInvocationHandler.invoke(AnnotationInvocationHandler.java:50)
	at $Proxy94.entrySet(Unknown Source)
	at sun.reflect.annotation.AnnotationInvocationHandler.readObject(AnnotationInvocationHandler.java:327)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
	at java.lang.reflect.Method.invoke(Method.java:597)
	at java.io.ObjectStreamClass.invokeReadObject(ObjectStreamClass.java:974)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1849)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1753)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1329)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:351)
	at weblogic.rjvm.InboundMsgAbbrev.readObject(InboundMsgAbbrev.java:65)
	at weblogic.rjvm.InboundMsgAbbrev.read(InboundMsgAbbrev.java:37)
	at weblogic.rjvm.MsgAbbrevJVMConnection.readMsgAbbrevs(MsgAbbrevJVMConnection.java:227)
	at weblogic.rjvm.MsgAbbrevInputStream.init(MsgAbbrevInputStream.java:212)
	at weblogic.rjvm.MsgAbbrevJVMConnection.dispatch(MsgAbbrevJVMConnection.java:442)
	at weblogic.rjvm.t3.MuxableSocketT3.dispatch(MuxableSocketT3.java:323)
	at weblogic.socket.BaseAbstractMuxableSocket.dispatch(BaseAbstractMuxableSocket.java:298)
	at weblogic.socket.SocketMuxer.readReadySocketOnce(SocketMuxer.java:901)
	at weblogic.socket.SocketMuxer.readReadySocket(SocketMuxer.java:840)
	at weblogic.socket.EPollSocketMuxer.dataReceived(EPollSocketMuxer.java:215)
	at weblogic.socket.EPollSocketMuxer.processSockets(EPollSocketMuxer.java:177)
	at weblogic.socket.SocketReaderRequest.run(SocketReaderRequest.java:29)
	at weblogic.socket.SocketReaderRequest.execute(SocketReaderRequest.java:42)
	at weblogic.kernel.ExecuteThread.execute(ExecuteThread.java:145)
	at weblogic.kernel.ExecuteThread.run(ExecuteThread.java:117)

Caused By: java.lang.NullPointerException
	at com.sun.org.apache.xalan.internal.xsltc.runtime.AbstractTranslet.postInitialization(AbstractTranslet.java:364)
	at com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl.getTransletInstance(TemplatesImpl.java:354)
	at com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl.newTransformer(TemplatesImpl.java:382)
	at com.sun.org.apache.xalan.internal.xsltc.trax.TrAXFilter.<init>(TrAXFilter.java:59)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:39)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:27)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:513)
	at org.apache.commons.collections.functors.InstantiateTransformer.transform(InstantiateTransformer.java:105)
	at org.apache.commons.collections.functors.ChainedTransformer.transform(ChainedTransformer.java:122)
	at org.apache.commons.collections.map.LazyMap.get(LazyMap.java:157)
	at sun.reflect.annotation.AnnotationInvocationHandler.invoke(AnnotationInvocationHandler.java:50)
	at $Proxy94.entrySet(Unknown Source)
	at sun.reflect.annotation.AnnotationInvocationHandler.readObject(AnnotationInvocationHandler.java:327)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
	at java.lang.reflect.Method.invoke(Method.java:597)
	at java.io.ObjectStreamClass.invokeReadObject(ObjectStreamClass.java:974)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1849)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1753)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1329)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:351)
	at weblogic.rjvm.InboundMsgAbbrev.readObject(InboundMsgAbbrev.java:65)
	at weblogic.rjvm.InboundMsgAbbrev.read(InboundMsgAbbrev.java:37)
	at weblogic.rjvm.MsgAbbrevJVMConnection.readMsgAbbrevs(MsgAbbrevJVMConnection.java:227)
	at weblogic.rjvm.MsgAbbrevInputStream.init(MsgAbbrevInputStream.java:212)
	at weblogic.rjvm.MsgAbbrevJVMConnection.dispatch(MsgAbbrevJVMConnection.java:442)
	at weblogic.rjvm.t3.MuxableSocketT3.dispatch(MuxableSocketT3.java:323)
	at weblogic.socket.BaseAbstractMuxableSocket.dispatch(BaseAbstractMuxableSocket.java:298)
	at weblogic.socket.SocketMuxer.readReadySocketOnce(SocketMuxer.java:901)
	at weblogic.socket.SocketMuxer.readReadySocket(SocketMuxer.java:840)
	at weblogic.socket.EPollSocketMuxer.dataReceived(EPollSocketMuxer.java:215)
	at weblogic.socket.EPollSocketMuxer.processSockets(EPollSocketMuxer.java:177)
	at weblogic.socket.SocketReaderRequest.run(SocketReaderRequest.java:29)
	at weblogic.socket.SocketReaderRequest.execute(SocketReaderRequest.java:42)
	at weblogic.kernel.ExecuteThread.execute(ExecuteThread.java:145)
	at weblogic.kernel.ExecuteThread.run(ExecuteThread.java:117)
>
```