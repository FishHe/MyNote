> 为了更好地理解交互式3D建模软件的构成，我先梳理B-Processor中重要的类和方法。
> 所有的UML图我都放在了UML文件夹中

**目录**

- [B-Processor的框架结构](#bpframe)

- [空间数据结构](#spacestructure)

- [绘图工具](#tools)

<a name="bpframe"></a>
# B-Processor的框架结构

<a name="bpmainframe"></a>
## bprocessor-主框架

b-processor中包含程序的主方法，另有重要的子空间：

* model：3D模型核心
* gl：openGL接口及模型工具
* gui：用户图形界面

![](assets/markdown-img-paste-20180907091141796.png)

## model-模型

所有模型都继承自实体类Entity，它包含了空间物体最一般的方法，相当于一个命名标签。

![](assets/markdown-img-paste-20180907093400984.png)

![](assets/markdown-img-paste-2018090709331963.png)

几何体的构建和传统3D系统类似。基础点边面结构、联合、辅助图形

* Vertex-Edge-Surface
* Item
  * Space
  * Instance
* Constructor

![](assets/markdown-img-paste-20180907094106843.png)

## undo/redo-撤销/重做机制

在Project类中，有两个栈，用于保存撤销重做的属性参数。在每一次可撤回操作中，所有的属性都被保存。下面这段代码大致解释了撤销原理：

![](assets/markdown-img-paste-2018090709512217.png)

    //保存当前状态
    public void checkpoint() {
        //将目前所有接口自Parametic的实体参数化
        final Bmodel state = Persistence.externalize();
        if (state != null) {
            this.redoStack.clear();
            if (this.currentState != null) {
                this.undoStack.push(this.currentState);
                if (this.undoStack.size() > 5) {
                    this.undoStack.remove(0);
                }
            }
            this.currentState = state;
        }
    }
    //撤销至上一状态
    public void undo() {
        if (this.canUndo()) {
            if (this.currentState != null) {
                this.redoStack.push(this.currentState);
            }
            this.setState(this.currentState = this.undoStack.pop());
            Selection.primary().clear();
        }
    }
    //重做至下一状态
    public void redo() {
        if (this.canRedo()) {
            if (this.currentState != null) {
                this.undoStack.push(this.currentState);
            }
            this.setState(this.currentState = this.redoStack.pop());
            Selection.primary().clear();
        }
    }

![](assets/markdown-img-paste-20180907100302503.png)

在MPC中，还是继续使用MPC自带的Document来保存操作，这里介绍重做机制只是帮助理解。


## gl.tool-交互工具
各类画笔、拓扑工具被放置在了gl包中。抽象画笔工具是几何工具和拓扑工具的基类。

![](assets/markdown-img-paste-20180907090115879.png)

<a name="spacestructure"></a>
# 空间数据结构
为了实现3D交互操作，将在MPC中使用B-Processor的数据结构。

## Space

Item表示空间中的一个物件，这个物件独立于其它点线面，但它又可以拆分为点线面。

Space继承于Item，它是一个点边面的联合。

![](assets/markdown-img-paste-20180907151518726.png)

![](assets/markdown-img-paste-20180907152808383.png)


## Vertex
顶点结构如常。有一个特殊的字段Corner，暂时不清楚含义。

![](assets/markdown-img-paste-20180907153252797.png)

对于每一个顶点，都可以在它最直接的上一层容器中，查找含有它的边：

    public Set<Edge> getEdges() {
        Set<Edge> result = new HashSet();
        if (this.getOwner() == null) {
            return result;
        } else {
            Collection edges = this.getOwner().getEdges();
            Iterator iter = edges.iterator();

            while(iter.hasNext()) {
                Edge current = (Edge)iter.next();
                if (current.contains(this)) {
                    result.add(current);
                }
            }

            return result;
        }
    }

同样也能通过查找它的Edge，来获取含有它的面：

    public Set<Surface> getSurfaces() {
        Set<Surface> result = new HashSet();
        Set edges = this.getEdges();
        Iterator iter = edges.iterator();

        while(iter.hasNext()) {
            Edge current = (Edge)iter.next();
            result.addAll(current.getSurfaces());
        }

        return result;
    }

## Edge
Edge包含起点和终点。

在BP中，这一结构只能描述直线段，为了克服这一缺点，在MPC中Edge应该能够兼容其它曲线表示。MPC中会支持多段直线（手绘线）、圆弧。

![](assets/markdown-img-paste-20180907160134773.png)

## Surface
Surface有一个边的集合来描述它的轮廓，有一个面的集合来描述它的洞。
如果当前的Surface是一个洞，exterior字段来记录它的外围，洞的外围有且仅有一个。

![](assets/markdown-img-paste-20180907162327411.png)

<a name="tools"></a>
# 绘图工具
绘图工具要进行复杂的拓扑变换操作，例如在空间中绘制一条线段，会破坏原有线段，也会破坏原有面片，可会完全改变以前的拓扑，也可能生成全新的拓扑。

## 抽象画笔
抽象画笔是所有画笔类工具的基类，它的主要功能包括：

* 产生临时坐标系，用于捕捉


![](assets/markdown-img-paste-20180907173153316.png)

## 直线画笔


![](assets/markdown-img-paste-20180907173256685.png)
