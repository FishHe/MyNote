> 结构设计会基于现有系统

> 应老板大人要求 我要将java中的b-processor移植到c++中

# 待修复的Bug

## OpenGL 多线程

下面的Bug在鼠标悬停一段时间后，出现辅助绘制工具时触发。

Bug分析：绘制辅助工具是由另一个计时器线程触发的。

Bug修复：除绘制线程外，其它线程不应当操作窗体绘制方法，只需向窗体的消息循环，注册绘制函数即可。

不同窗体的消息循环机制不同，但都应提供了“重绘函数”。


```

javax.media.opengl.GLException: No OpenGL context current on this thread
        at javax.media.opengl.glu.GLU.getCurrentGL(GLU.java:234)
        at javax.media.opengl.glu.GLU.gluPerspective(GLU.java:1188)
        at net.sourceforge.bprocessor.exgl.view.View.initCamera(View.java:899)
        at net.sourceforge.bprocessor.exgl.view.View.draw(View.java:674)
        at net.sourceforge.bprocessor.exgl.view.View.display(View.java:507)
        at net.sourceforge.bprocessor.exgl.ExternalEditor.display(ExternalEditor.java:226)
        at net.sourceforge.bprocessor.exgl.tool.AbstractPencil.hoover(AbstractPencil.java:620)
        at net.sourceforge.bprocessor.exgl.tool.AbstractPencil$TargetAction.actionPerformed(AbstractPencil.java:556)
        at java.desktop/javax.swing.Timer.fireActionPerformed(Timer.java:317)
        at java.desktop/javax.swing.Timer$DoPostEvent.run(Timer.java:249)
        at java.desktop/java.awt.event.InvocationEvent.dispatch(InvocationEvent.java:313)
        at java.desktop/java.awt.EventQueue.dispatchEventImpl(EventQueue.java:770)
        at java.desktop/java.awt.EventQueue.access$600(EventQueue.java:97)
        at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:721)
        at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:715)
        at java.base/java.security.AccessController.doPrivileged(Native Method)
        at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:87)
        at java.desktop/java.awt.EventQueue.dispatchEvent(EventQueue.java:740)
        at java.desktop/java.awt.EventDispatchThread.pumpOneEventForFilters(EventDispatchThread.java:203)
        at java.desktop/java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:124)
        at java.desktop/java.awt.EventDispatchThread.pumpEventsForHierarchy(EventDispatchThread.java:113)
        at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:109)
        at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:101)
        at java.desktop/java.awt.EventDispatchThread.run(EventDispatchThread.java:90)

```
