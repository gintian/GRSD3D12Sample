# 修订记录：

## 2021-6-2：

1、修改了03-D3D12PlacedTexture示例中的代码，删除了d3dx12.h头文件，并且把所有引用自该文件中的类、方法等都改回了最原始的d3d12中的结构体和函数的引用，这主要是因为现在VS2019中的C++编译器标准已经升级，不在支持那种嵌入的从类中返回临时对象并取其地址作为参数的情况，代码编译中会报错，所以干脆把所有这些引用全部删除，后续的项目也会逐步的删除关于d3dx12.h所有引用。1、2示例中之前就已经去掉了，当时是为了保证大家都能接触到最原汁原味的D3D12结构体、函数、接口等。

2、修改了15-CPUThreadsBarrier示例的窗口标题。

3、现在的DXGI在升级之后，当刚创建完时，就使用MakeWindowAssociation函数关闭Alt+Enter键转全屏时，就会引发一个DXGI的警告，它会抱怨说还不知道窗口是哪个，所以将这个调用调整到了创建交换链完成之后，这时候实质上DXGI就知道了我们操作的窗口是哪一个。

4、现在除了多显卡渲染的几个例子之外，其它所有的例子都会频繁报：

```sh
D3D12 ERROR: ID3D12CommandQueue::Present: Resource state (0x800: D3D12_RESOURCE_STATE_COPY_SOURCE) (promoted from COMMON state) of resource (0x000001B737E63E00:'Unnamed ID3D12Resource Object') (subresource: 0) must be in COMMON state when transitioning to use in a different Command List type, because resource state on previous Command List type : D3D12_COMMAND_LIST_TYPE_COPY, is actually incompatible and different from that on the next Command List type : D3D12_COMMAND_LIST_TYPE_DIRECT. [ RESOURCE_MANIPULATION ERROR #990: RESOURCE_BARRIER_MISMATCHING_COMMAND_LIST_TYPE]

```

这个错误，这是Windows内部的问题，跟我们的例子无关，我目前分析大概的原因是因为像我这个笔记本因为有两个显卡，而显示器又默认连接在集显上，可是我们的例子都是在独显上渲染的，这样就会有一个最终隐藏的需要将渲染结果从独显复制到核显上的过程，而Windows内部貌似现在没有处理好这个资源状态的切换，所以就会频繁报这个#990的错误，但是当我们自己控制多个显卡的渲染，并控制共享纹理和渲染目标资源状态（资源屏障同步）时，或系统中只有一个显卡，并且显示器直接接在这个唯一显卡上时都不会报这个错误。这个目前我还没有找到修正的方法，或许只能等到Windows更新D3D12相关的模块了。但这个问题不影响例子的运行，所以现在就先忽略吧。

5、修正了Readme文件中图片显示异常的问题。



2021-6-2

1、将3、4示例的窗口标题修改正确了，之前是复制粘贴过来的代码进行修改，一直没有修改前几章的窗口标题；

2、继续从示例4、5中去除d3dx12.h的所有引用；

3、新发现一个问题，在枚举显卡过程并创建D3D12设备对象时，如果是核显的时候会报一个内部错误：

```sh
0x00007FFBAB0B491C 处(位于 05-SkyBox.exe 中)引发的异常: Microsoft C++ 异常: MONZA::Exception，位于内存位置 0x0000000600D989F4 处。
```

这个情况主要出现在当系统中有多个显卡，并且是Intel核显的时候，默认在系统中0号显卡就是这个核显，目前可能是因为驱动的原因导致这个异常，直接忽略继续执行即可，当然如果你有两块显卡，并且1号显卡是独显的时候，那么就将循环变量的起始值设为1即可，在我的电脑Nvidia的独显不会报此问题。

2021-6-4

1、添加了16-Assimp_Data_Display示例，用来以命令行方式直接显示Assimp导入的模型数据。

2、去除了示例6中所有关于d3dx12.h的所有引用；

2021-6-5

1、去除了示例7多显卡渲染中的d3dx12.h的所有引用；

2、去除了示例8 UI渲染中的d3dx12.h的所有引用；

2021-6-6

1、去除了示例9渲染到纹理示例中所有d3dx12.h的引用；

2、去除了示例10几个后处理特效示例中所有d3dx12.h的引用；

2021-6-7

1、去除了示例11多线程+多显卡渲染示例中所有d3dx12.h的引用；

2021-6-8

1、去除了示例12多线程 + 多显卡渲染 + D2D + DirectWrite示例中所有d3dx12.h的引用；

2021-6-9

1、去除了示例14多线程阴影渲染示例中所有d3dx12.h的引用；目前阴影矩阵还有点问题，没有来及修复，明天看时间调试修复一下；

2021-6-10

1、添加了Assimp导入3D模型，并运行3D骨骼动画的第17号示例；

2021-6-13

1、修正了GRS_Texture_Loader.h中用WIC库加载纹理的BUG，之前在WIC加载失败时，Load函数任然会返回TRUE，并且在判断是否需要图片格式兼容性转换时逻辑也是错的，现在改为首先判断能否直接加载，然后再判断是否可以做兼容性转换，然后再去转换加载。

2、添加并修正了简单的摄像机控制逻辑，这个目前还不是介绍的重点，所以没有做严格的控制。

2021-6-14

1、将17号例子中的背景色改成了天空蓝，看上去舒服一些；

2021-6-16

1、调整了16号例子中的输出格式，并且追加了显示metadata的代码；

2021-6-18

1、去除17号示例中的冗余代码，修正了部分注释；

2021-6-19

1、修正17号示例中，计算缓冲大小错误的BUG，之前的计算在顶点数据小时不会有问题，当顶点数据过大时就报错了，原因是每个缓冲都按照边界对齐分配了，但是缓冲是整体按边界对齐大小分配的，所以在某个缓冲较大时就会出错了，实际分配的缓冲大小会小于需要的缓冲大小；

2021-6-22

1、添加第18个示例，18-PBR-Base-Point-Lights，开启高级光照之旅，直接略过无聊的传统光照；

2021-6-25

1、修正了第18号示例Shader中的BUG，之前用的是二次衰减，因为开始已经Gamma矫正了，二次衰减的话会使衰减过度，所以改成1次衰减，这样Gamma矫正的2.2次方近似等于二次方了，这样相当于已经二次衰减了。

2、14号示例还是没有修正好，先提交，大家可以先略过；重点是修改了18号示例中光线衰减计算的错误，并且更新了18号示例的说明；

2021-7-14

1、删除示例17中，ReadNodeHeirarchy函数中冗余的调试代码，以免大家阅读代码时产生误解。

2021-7-15

1、修正17号例子中Layout变量的命名，以免引起歧义。

2021-7-21

1、将所有之前EnumAdapters1枚举显卡的循环变量改回从0开始，防止好多系统都没法运行示例，大家都不知道怎么回事；

2021-9-11

1、一口气添加4个项目（19-22），前三个是空的框架性项目，主要是方便大家在此基础上编写自己的示例，主要是逐步加入了Resize和Fullscreen的支持，Fullscreen使用传统的Alt+Enter键来切换，当然你可以用别的方法控制，函数就在那里，等你调用；

2、22号项目是基于点光源PBR渲染的延续，使用了非常重要的多实例以及多插槽（Multi-Instance、Multi-Slot）方法。

2021-12-27

1、在修改14号示例阴影部分时，需要加载DDS纹理，包括颜色纹理和法线纹理的DDS，在另一个例子中使用了更方便的WIC方式来加载DDS，结果发现Cube的DDS和Plane的DDS都无法加载，最后直接用VS2019打开后发现，这两个DDS使用的编码格式是BGRA格式，这样就需要把它们转存为BC3编码格式，目前WIC已经可以支持到最高BC3压缩方式的DDS纹理，这样就比较方便统一纹理的加载方式。所以最终将Cube和Plane的DDS纹理都转码为BC3方式存储。同时BC3编码格式的DDS纹理体积也变小了，另外在Windows资源管理器中也可以预览了，因为Windows资源管理器使用的解码器与WIC使用的解码器是一致的。
