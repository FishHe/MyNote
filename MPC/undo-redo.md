# MPC中的撤销与重做

## 增删改
MPC用树结构来管理数据。文档结构使用全局函数获取，之后在文档结构上进行修改：

```cpp
//获取文档结构
CMPCView* pview = (CMPCView*)GetActiveView();
auto pDoc = pview->pFreecadDoc;
//在文档中添加一个“FishHe::FeatureFishHeFace”类型的节点
auto poly = pDoc->addObject("FishHe::FeatureFishHeFace");
auto fishhe = dynamic_cast<FishHe::FeatureFishHeFace *>(poly);
//设置该节点的Shape属性（OpenCasCade接口）
fishhe->Shape.setValue(aFace);
//设置显示属性（Coin3d接口）
auto viewprvd = pDoc->getViewProvider(poly);
viewprvd->setDisplayMode("Shaded");
```

经过以上操作，可以向当前场景中增加、删除、修改物体。

## 事务

MPC可以记录所有增加、删除、修改的“事务”（Transaction，或称动作）。可以调用**录制**命令来记录一个事务。

```cpp
//获取文档结构
CMPCView* pview = (CMPCView*)GetActiveView();
auto pDoc = pview->pFreecadDoc;
//开始记录，“ClosePoly”是当前事务的名称
pDoc->openTransaction("ClosePoly");

/////////////////////////
//一些增删改操作
/////////////////////////

//完成记录
pDoc->commitTransaction();

```

位于**openTransaction**与**commitTransaction**之间的增删改操作全部被记录。

## 撤销与重做

通过撤销重做，来管理事务。

```cpp
void MPC::CMainFrame::OnFishhetoolUndo()
{
	// TODO:  在此添加命令处理程序代码
	CMPCView *pView = (CMPCView*)GetActiveView();
	pView->pFreecadDoc->undo();
	Invalidate(TRUE);
}
```

```cpp
void MPC::CMainFrame::OnFishhetoolRedo()
{
	// TODO:  在此添加命令处理程序代码
	CMPCView *pView = (CMPCView*)GetActiveView();
	pView->pFreecadDoc->redo();
	Invalidate(TRUE);
}
```
