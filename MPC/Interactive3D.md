> 结构设计会基于现有系统

> 应老板大人要求 我要将java中的b-processor移植到c++中


# ExternalEditor

BProcessor中已有Editor类，负责管理视图，为了在C++中，调用BPro，我改该类为ExternalEditor

ExternalEditor要处理的消息：
* 传递C++消息到Bpro
* 窗体消息传递
  - init（已完成）
  - reshape（已否决）
  - display（已完成）
  - view model changed （相机状态改变）
    - 正交投影（已完成）
    - 透视投影（已完成）
* 键鼠消息传递
  - pressed（已完成）
  - released（已完成）
* 工具消息传递



# 待修复的Bug

## 颜色调整

## 鼠标停留后，需要重绘

参考：
Invalidate
InvalidateRect
InvalidateRgn

## 比例尺调整（已完成）

现在将比例尺统一到和inventor的比例尺一致。

## 平移与旋转（已完成）

与m_trans同步，将m_trans属性，赋值给BPro的Camera。需要注意：

* 相机运动是物体运动的逆
* 物体的缩放变换需要在其它参数中表现

## 透视投影Bug（已完成）
正交投影？透视投影？

在MPC中可以设置，暂时设置为正交投影，方便调试

## 缩放（已完成）

MPC中的缩放通过m_trans传递给物体，BPro中的缩放？

## OpenGL 多线程

> 此Bug暂时不用修复，MPC没有采用多线程/双缓存机制来优化显示

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
