# Java Event

> 这篇笔记用来说明Java的事件处理，并将c++中捕获到的键鼠事件，传递到java中。

## 模拟事件

要在Java中模拟一次事件，就要建立一个事件实例，下面是模拟一次鼠标事件的方法：

```java
//事件源
Canvas aComponet = new Canvas();
//事件
MouseEvent e = new MouseEvent(aComponet, MouseEvent.MOUSE_MOVED,1, 0, x, y,0,false);
//触发事件
aComponet.dispatchEvent(e);
```


为了解MouseEvent中各参数的意义，建议参考构造函数，MouseEvent的构造函数有3个，我只贴出其中一个：

```java
/**
 * Constructs a {@code MouseEvent} object with the
 * specified source component,
 * type, modifiers, coordinates, click count, and popupTrigger flag.
 * An invocation of the form
 * {@code MouseEvent(source, id, when, modifiers, x, y, clickCount, popupTrigger)}
 * behaves in exactly the same way as the invocation
 * {@link #MouseEvent(Component, int, long, int, int, int,
 * int, int, int, boolean, int) MouseEvent(source, id, when, modifiers,
 * x, y, xAbs, yAbs, clickCount, popupTrigger, MouseEvent.NOBUTTON)}
 * where xAbs and yAbs defines as source's location on screen plus
 * relative coordinates x and y.
 * xAbs and yAbs are set to zero if the source is not showing.
 * This method throws an {@code IllegalArgumentException}
 * if {@code source} is {@code null}.
 *
 * @param source       The {@code Component} that originated the event
 * @param id              An integer indicating the type of event.
 *                     For information on allowable values, see
 *                     the class description for {@link MouseEvent}
 * @param when         A long integer that gives the time the event occurred.
 *                     Passing negative or zero value
 *                     is not recommended
 * @param modifiers    a modifier mask describing the modifier keys and mouse
 *                     buttons (for example, shift, ctrl, alt, and meta) that
 *                     are down during the event.
 *                     Only extended modifiers are allowed to be used as a
 *                     value for this parameter (see the {@link InputEvent#getModifiersEx}
 *                     class for the description of extended modifiers).
 *                     Passing negative parameter
 *                     is not recommended.
 *                     Zero value means that no modifiers were passed
 * @param x            The horizontal x coordinate for the mouse location.
 *                       It is allowed to pass negative values
 * @param y            The vertical y coordinate for the mouse location.
 *                       It is allowed to pass negative values
 * @param clickCount   The number of mouse clicks associated with event.
 *                       Passing negative value
 *                       is not recommended
 * @param popupTrigger A boolean that equals {@code true} if this event
 *                     is a trigger for a popup menu
 * @throws IllegalArgumentException if {@code source} is null
 * @see #getSource()
 * @see #getID()
 * @see #getWhen()
 * @see #getModifiers()
 * @see #getX()
 * @see #getY()
 * @see #getClickCount()
 * @see #isPopupTrigger()
 */
 public MouseEvent(Component source, int id, long when, int modifiers,
                  int x, int y, int clickCount, boolean popupTrigger) {
    this(source, id, when, modifiers, x, y, clickCount, popupTrigger, NOBUTTON);
 }
```

## 从GLUT中捕获事件

从GLUT窗口中，捕获鼠标移动事件的方法：

```cpp
	glutPassiveMotionFunc(mouseMoved);
```

在mouseMoved函数中，调用定义好的事件接口：

```cpp
void mouseMoved(int x, int y)
{
	env->CallVoidMethod(jo_ee, jmid_mouseMoved, x, y);
}
```

在java中，模拟并触发事件：

```java
public void mouseMoved(int x, int y)
{
    MouseEvent e = new MouseEvent(glcomponet, MouseEvent.MOUSE_MOVED,1, 0, x, y,0,false);
    glcomponet.dispatchEvent(e);
}
```
