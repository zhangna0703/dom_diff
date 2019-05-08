<img src='https://img-blog.csdn.net/20180717182348969' alt=''>

DIFF算法在执行时有三个维度，分别是Tree DIFF、Component DIFF和Element DIFF，执行时按顺序依次执行，它们的差异仅仅因为DIFF粒度不同、执行先后顺序不同。 

<img src="https://img-blog.csdn.net/2018071718264787" alt="" title="">

Tree DIFF是对树的每一层进行遍历，如果某组件不存在了，则会直接销毁。如图所示，左边是旧属，右边是新属，第一层是R组件，一模一样，不会发生变化；第二层进入Component DIFF，同一类型组件继续比较下去，发现A组件没有，所以直接删掉A、B、C组件；继续第三层，重新创建A、B、C组件。

### 列表节点的比较
相信很多使用React的开发者大多遇到过这样的警告
<img src='https://upload-images.jianshu.io/upload_images/5899414-c893ce022eff9039.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/522/format/webp' alt=''>

这是React在遇到列表时却又找不到key时提示的警告。虽然无视这条警告大部分界面也会正确工作，但这通常意味着潜在的性能问题。因为React觉得自己可能无法高效地去更新这个列表

```
列表节点的操作通常包括添加、删除和排序。例如下图，我们需要往B和C直接插入节点F，在jQuery中我们可能会直接使用$(B).after(F)来实现。而在React中，我们只会告诉React新的界面应该是A-B-F-C-D-E，由Diff算法完成更新界面。
```

<img src='https://upload-images.jianshu.io/upload_images/5899414-10651c665bf68812.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/477/format/webp'>

这时如果每个节点都没有唯一的标识，React无法识别每一个节点，那么更新过程会很低效，即，将C更新成F，D更新成C，E更新成D，最后再插入一个E节点。效果如下图所示：

<img src='https://upload-images.jianshu.io/upload_images/5899414-8ad3a3d46efa0ac1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/572/format/webp'>

可以看到，React会逐个对节点进行更新，转换到目标节点。而最后插入新的节点E，涉及到的DOM操作非常多。diff总共就是移动、删除、增加三个操作，而如果给每个节点唯一的标识（key），那么React优先采用移动的方式，能够找到正确的位置去插入新的节点，如下图所示：

<img src='https://upload-images.jianshu.io/upload_images/5899414-e32d08d9a7fd7ca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/565/format/webp'>