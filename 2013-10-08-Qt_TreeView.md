{

title: "Tree view at Qt"

tags: ["qt"] 
} 

引言
----
> Model/View/item的关系就有点像Container/Algorithm/Iterator的关系。
> Tree view属于Model/View Programming的一部分。有些
> 

Index 带上下层级结构的二维索引 
----
QModelIndex()表示model中最上层的root item的index, QModelIndex().isValid() == 0. 

item之前有上下层级关系，index.parent()获取index的parent. 当index.parent().isValid() == false，表示这个item的parent就是root item. 这些items被称为top-level itmes. 

to retrieve data from a model:
for (int row = 0; row < model->rowCount(parentIndex); ++row)
{
	for (int col = 0; col < model->columnCount(parentIndex); ++col)
	{
		QString text = model->data(index, Qt::DisplayRole).toString();
        // Display the text in a widget.
	}
}
对于tree view中的每一个row，它们的column数目都是一样的吗？
每一个index的role的类型都一样? 请看是否有QVariant CustomModel::data(const QModelIndex &, int role) const {} 貌似一个index里面有各种各样的role，你可以放什么东西都ok，只要你在这个函数中给对应的role返回合适的数据。这个函数在代码非常常用！例如后面CustomDelegate::paint()中就是根据data值来判断怎么画和画什么的。
Question: QVariant QModelIndex::data(role); 这个函数跟model->data(index, role)结果一样的吗? 这里有个小细节, model是指针才能data()函数override. 
跟data(...)函数的对应的另一函数是CustomModel::setData(...); 这个函数一般用的地方不多，就在给model插入新行insertRow()之后给每一个index插入不同的数据时候会用到。

Delegate 满足你特别的图案和交互需要 
----
前提: install a custom delegate for a view. 
任务: render and editting. 

To render:
	Delegate::paint().
	
To provide editing facilities:
	QWidget *Delegate::createEditor(QWidget *, ..., const QModelIndex &);
	为某一个 or 每一个index提供editor? Can we create different editors depending on the model index supplied by the view ? 假如某些不行被edit而只是空白呢? 
	
	这些new QWidget obj应该是被view所manager的，本来可能就是view->delegate->createEditor for a given index.
	
	一旦在这里给某个item/index建立了一个QWidget obj以后，那个view貌似就会自动把这个index跟它的QWidget obj关联起来，我们不需要担心，而是考虑有了这个关联之后怎么来 (a) copy the model data into the editor, Delegate::setEditorData(QWidget *, const QModelIndex &) const, 参数的两者就是view给我们的, index跟 widget已经一一对应好的. (b) when user has finished editting the value in the widget, the view asks the delegate to store the edited value in the model by calling Delegate::setModelData(QWidget *, model, index) const; 这里的(a and b)就是两个方向editor <-> model/index.  
	
Question: in the delegate class, how to get its view ? try to get the QObject *parent() first and cast it to the QTreeView or custom view type.
	
	
Render
----
Scenior: 一般是在自定义CustomModel, CustomView, CustomeDelegate的之后才考虑自己要怎么画Tree view中的东西. 

CustomView::paintEvent(QPaintEvent *)
	drawTree(...);
		drawRow(QPainter *, const option, const index); 干什么的呢?
			貌似可以选择给特定的index (item)填一些背景, 然后QTreeView(...)做默认画。
			drawBranches(QPainter *, const QRect &rect, const index) const;
			delegate->paint(...);
			
可见首先是一行一行地画出来的. 先画branch分枝，然后在delegate->paint()画内容。
D:\Qt\Qt5.1.0\5.1.0\Src\qtbase\src\widgets\itemviews\qtreeview.cpp for details.

darwBranches(..., const QRect &rect, ...)
	在分枝中下一级会相对于上一级做一个往右边缩进，setIndentation(int)函数控制缩进的多少。而rect这个区域是做这一行的左边往右边加上每一次的缩进所得。最上一层top-level 是从root-item缩进一级所得。而且expand/collapse的event都是只在画branch这个区域上响应的。
	问题来了，上面的缩进是所以level都一样的，可否不同level用不同大小的缩进呢? 未解。
example:
--level0
----level1
------level2
the indentation for this is 2 (--). 

假如想把level0前面的缩进去掉，试试QTreeView::setRootIsDecorated(false);



Read-Only access : (Qt::ItemIsEnabled | Qt::ItemIsSelectable)
Editable items: Qt::ItemIsEditable | (Qt::ItemIsEnabled | Qt::ItemIsSelectable) 是在Read-Only access基础上加的. 
这里有一点观察比较重要，假如flag里面不包含Qt::ItemIsEnabled, 那么这个item基本上不能干啥的了, 例如不能editable。 

Drag & Drop: 把一个item drag到另一个item上面，有些是接受，有些是不接受的，为什么呢？
Model::flags(const QModelIndex &) contain the Qt::ItemIsDragEnabled and Qt::ItemIsDropEnabled. 前提当然是已经包括上面提到的(Qt::ItemIsEnabled | Qt::ItemIsSelectable),我试过把Qt::ItemIsEnabled去掉，这个item就不能被选择了, 貌似就不响应mouse event了。
在View里面还有一些设置setDragDropMode(QAbstractItemView::DragDrop); setDragEnabled(true); setAcceptDrops(true); setDropIndicatorShown(true);
debug时候看到具体的函数流是: mouse move -> mouseMoveEvent() -> 按着LMB拖动-> startDrag() -> dragEnterEvent() -> dragMoveEvent() -> drop the item -> dropEvent()


