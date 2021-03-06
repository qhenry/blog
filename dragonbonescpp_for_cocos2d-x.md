[DragonBones 官方C++版本 for cocos2d-x](http://zengrong.net/post/2106.htm)

DragonBonesCPP for cocos2d-x

## DragonBones 和 CCArmature

[DragonBones][1] 是一套骨骼动画工具集，最早使用 Flash 和 ActionScript 3.0 语言开发，主要在 Flash 游戏中使用，目前在页游和手游项目中使用很广泛。

DragonBones 目前包含下面这些内容：

1. 基于 Flash IDE 中时间轴动画的编辑工具（美术GGMM的最爱）；
2. [基于 Flash IDE 插件的骨骼动画设计面板 DesignPanel][13]；
3. [基于 ActionScript 3.0 的骨骼动画解析库和渲染库][12]；
4. [基于 Javascript 的骨骼动画解析库和渲染库][15]；
5. [基于 Unity3D C# 脚本的骨骼动画解析库和渲染库][16]；
6. [基于 LoomScript 的骨骼动画解析库和渲染库][14]。

详细的入门介绍可以看这里：<!--more-->[DragonBones快速入门指南][3] 。

cocos2d-x 中自带的 CCArmature 移植自 DragonBones 2.1 版本，我对 DragonBones Design Panel进行了修改 [cocos2d-x专用的DragonBones2.2][4] ，使其直接输出让 CCArmature 支持的资源格式。

## CCArmature 的问题

CCArmature 的移植并不完善，主要有下面这些问题：

1. 不支持逐帧动画；
2. 对嵌套骨骼支持不力；
3. 没有实现关键帧事件；
4. 不支持将某个Bone替换成逐帧动画；
5. 完全针对 cocos2d-x 优化，与DragonBones的API不兼容；
6. [cocos2d-x中CCArmature展示挤压和变形动画的问题][5] ；
7. 等等等…… 

另外，为了支持 cocostudio 的特性，CCArmature 进行了许多不兼容 DragonBones 的修改，导致新版本的 CCArmature 对 DragonBones Design Panel 生成的资源格式的支持也不好了。详情见：[在cocos2d-x中使用DragonBones实现骨骼动画][2] 。

## DragonBonesCPP

正是因为这些问题，[DragonBones 开发组][10] 使用 C++ 移植了 DragonBones 的解析库和动画渲染库，命名为 [DragonBonesCPP][6] 。它的特点如下：

1. 这套库完全与 ActionScript 3 库保持一致，可以实现 ActionScript 3 库的所有功能；
2. 使用 DragonBones 官方的 Design Panel，不再需要使用专门针对cocos2d-x的修改版，可保持数据格式与最新版兼容；
3. 由于支持官方 DragonBones Design Panel 输出的格式，因此也可以支持 [Spine][11] 的格式；
3. 支持多套渲染引擎，不再仅限于cocos2d-x。

目前，DragonBonesCPP 首先完成了对 cocos2d-x 引擎的支持。下面是一些Demo：

* [DragonBonesCPP for cocos2d-x 2.x][7]
* [DragonBonesCPP for cocos2d-x 3.x][8]
* [DragonBonesCPP for quick-cocos2d-x][9]

已知的问题：

1. DragonBones Design Panel 可以导出多种资源格式，但 DragonBonesCPP 仅支持 XML+PNG 格式；
2. 有一些内存泄露；
3. 没有做异步资源加载，可能会导致在加载资源时候的性能问题。

## 更进一步

DragonBones 开发组将继续更新该库，解决现有问题，并保证对 cocos2d-x 引擎的兼容。

喜欢用 Flash 编辑骨骼动画（或者讨厌用CCS编辑骨骼动画）的同学们，是时候了！

[1]: http://dragonbones.github.io/
[2]: http://zengrong.net/post/1911.htm
[3]: http://dragonbones.github.io/DBGettingStarted_V2.0_cn.html
[4]: http://zengrong.net/post/1915.htm
[5]: http://zengrong.net/post/1922.htm
[6]: https://github.com/DragonBones/DragonBonesCPP
[7]: https://github.com/DragonBones/DragonBonesCPP/tree/dev/demos/cocos2d-x-2.x
[8]: https://github.com/DragonBones/DragonBonesCPP/tree/dev/demos/cocos2d-x-3.x
[9]: https://github.com/zrong/quick-cocos2d-x/tree/zrong/samples/dragonbones
[10]: https://github.com/DragonBones
[11]: http://esotericsoftware.com/
[12]: https://github.com/DragonBones/DragonBonesAS
[13]: https://github.com/DragonBones/SkeletonAnimationDesignPanel
[14]: https://github.com/DragonBones/DragonBonesLoomScript
[15]: https://github.com/DragonBones/SkeletonAnimationLibraryJS
[16]: https://github.com/DragonBones/DragonBonesUnity
