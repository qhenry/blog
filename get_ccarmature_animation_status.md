获取CCArmature动画的播放状态

本文介绍如何在 CCArmature 播放的过程中获取动画的播放状态。例如，如何判断 “死亡” 动作已经播放完毕了？如果知道一个 “循环” 动作已经播放了一次？，提纲如下：

1. 先决条件；
2. 必要知识；
3. MovementEventSignal 的用法。
<!--more-->
**本文基于 cocos2d-x 2.1.5** 

## 一、 先决条件

1. 代码基于 cocos2d-x 2.1.5；
2. 本文假设你已经会使用CCArmature，若不会，请看这里： [在cocos2d-x中使用DragonBones实现骨骼动画][usage] 。

## 二、 必要知识

CCArmature是cocos2d-x中包含的一个扩展功能，用来实现骨骼动画。它是从DragonBones 2.2移植而来。

要使用CCArmature来播放骨骼动画，可以查看这里：[在cocos2d-x中使用DragonBones实现骨骼动画][usage] 。

DragonBones使用Actionscript 3写成。在 [DragonBones的文档][dbevent] 中，我们可以看到Armature提供了一组事件 `boneFrameEvent/complete/loopComplete/movementChange/movementFrameEvent/start` 来通知动画状态的改变。

那么，CCArmature这套C++移植版是怎么实现这些事件的？答案是： [sigslot][sigslot] 

sigslot 是一套 C++ 实现的调用机制，在 [Qt][qt] 和 [Boost][signals] 中都有应用。Actionscript 3中也有它的一个实现，我曾经介绍过： [Signals框架介绍][as3signals] 。

sigslot 是一个合成词，由 Signals+Slot合成。翻译成中文就是 “信号槽”。信号（Signals）负责发布事件，槽（Slot）负责接收发布的事件。在使用时，通过信号提供的connect方法连接一个信号和一个/多个槽。

CCArmatureAnimation 中定义了 MovementEventSignal 和 FrameEventSignal 这两个信号（Signals）来发布状态改变事件，但没有连接槽（Slot）进行处理。从源码来看，FrameEventSignal 只是做了声明，既没有实现，也没有使用。

## 三、 MovementEventSignal 的用法
<pre lang="CPP">
// in ArmaturePlayer.h
#include "CCArmature/external_tool/sigslot.h"
//... other CCArmature package #include
class ArmaturePlayer : public cocos2d::CCLayer , public sigslot::has_slots<>
{
	//... your implemention
	void handler_movementEvent(cocos2d::extension::CCArmature *, cocos2d::extension::MovementEventType, const char *);
}

// in ArmaturePlayer.cpp

_pArmature = CCAramture::create("hero");
_pArmature->getAnimation()->MovementEventSignal.connect(this, &ArmaturePlayer::handler_movementEvent);

void ArmaturePlayer::handler_movementEvent(CCArmature* $pArmature, MovementEventType $evtType, const char* $movId)
{
	CCLOG("movementEvent, evtType:%d, moveID: %s", $evtType, $movId);
	if($evtType == MovementEventType::START)
		CCLOG("the movement %s is starting!", %movId);
	else if($evtType == MovementEventType::COMPLETE)
		CCLOG("the movement %s has completed!", %movId);
	else($evtType == MovementEventType::LOOP_COMPLETE)
		CCLOG("the movement %s has loop completed!", %movId);
}</pre>
* 本文假设读者已经会使用CCArmature，因此省略了CCArmature的包含部分，但对 `sigslog.h` 头文件的包含进行了强调；
* 使用connect方法连接信号（Signals）和槽（Slot），这个方法接受两个指针：槽对象的指针，和槽处理器函数的指针；
* 实现槽处理器的类，必须继承 `sigslot::has_slots<>` 这个模版；
* 槽处理器函数不能有返回值，即必须返回void；
* 槽处理函数的三个参数分别为：发出信号的CCArmature对象指针、动画事件类型、动画id（动作的名称字符串）。

要了解 sigslot 的具体用法，这里有一篇详细的文章：[Sigslot介绍][usagesigslot] 。

[usage]: http://zengrong.net/post/1911.htm
[dbevent]: http://dragonbones.github.io/asdoc/V2/dragonBones/Armature.html
[sigslot]: http://sigslot.sourceforge.net/
[usagesigslot]: http://blog.csdn.net/smallcraft/article/details/2237802
[signals]: http://www.boost.org/doc/libs/1_54_0/doc/html/signals.html
[qt]: http://qt-project.org/
[as3signals]: http://zengrong.net/post/1504.htm
