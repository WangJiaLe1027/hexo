---
tag: vr
title: SceneKit虚拟现实和3D图形
---
上期分享中实现了使用CoreMotion获取设备运动状态，从而实现交互数据准备。
本期要简单介绍一下[SceneKit](https://developer.apple.com/library/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html)，用这个图形框架实现虚拟现实的立体图像，以及图像对运动的响应。

● SCNView：显示SceneKit内容的视图

● SCNScene：加载其他工具构建的的3D文件场景，或者自己编写的场景，用于显示这个场景。

● SCNNode：一个场景的基本层次结构，节点可以添加几何形状，灯光，摄像机等……

● SCNGeometry：

● SCNMaterial：表面外观特征，指定材料的表面着色纹理，以及如何响应场景灯光。

● SCNLight：一个节点，在渲染场景中提供着色的光源。

● SCNCamera：一个节点，场景观察视角的摄像机。
<!--more-->
## 快速入门Demo0
新建一个Xcode工程
![img](http://7xrxzy.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-08-09%2017.45.56.png)
![img](http://7xrxzy.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-08-09%2017.46.29.png)
选择SceneKit，就会初始化一个Demo。
我们称之为Demo0吧，这是一个简单的开始，通过了解Demo中的代码你可以快速了解SceneKit具体能干什么。

``` bash

    //加载一个场景scene
    SCNScene *scene = [SCNScene sceneNamed:@"art.scnassets/ship.scn"];
    
    //给场景scene添加一个摄像机节点
    SCNNode *cameraNode = [SCNNode node];
    cameraNode.camera = [SCNCamera camera];
    [scene.rootNode addChildNode:cameraNode];
    
    //设置摄像机的位置
    cameraNode.position = SCNVector3Make(0, 0, 15);
    
    //给场景scene添加一个光照节点
    SCNNode *lightNode = [SCNNode node];
    lightNode.light = [SCNLight light];
    lightNode.light.type = SCNLightTypeOmni;
    lightNode.position = SCNVector3Make(0, 10, 10);
    [scene.rootNode addChildNode:lightNode];
    
    //给场景scene添加一个环境光节点
    SCNNode *ambientLightNode = [SCNNode node];
    ambientLightNode.light = [SCNLight light];
    ambientLightNode.light.type = SCNLightTypeAmbient;
    ambientLightNode.light.color = [UIColor darkGrayColor];
    [scene.rootNode addChildNode:ambientLightNode];
    
    //self.view 转换为SCNView
    SCNView *scnView = (SCNView *)self.view;
    
    //在scnView上添加一个场景scene
    scnView.scene = scene;
    
    //允许触屏控制摄像机位置
    scnView.allowsCameraControl = YES;
     //取出Node ship
    SCNNode *ship = [scene.rootNode childNodeWithName:@"ship" recursively:YES];
    //给ship添加动画。
    [ship runAction:[SCNAction repeatActionForever:[SCNAction rotateByX:0 y:2 z:0 duration:1]]];
   
    scnView.backgroundColor = [UIColor blackColor];
```
这样的代码即可实现一个简单的3D场景，SCNScene负责加载场景，摄像机SCNCamera负责实现确定视角，SCNLight负责光照，整个结构以节点SCNNode这种形式组织，通过这个官方的例子，很快就可以入门SceneKit 3D开发。

## Scene Kit概要
Scene Kit 建立在 OpenGL 的基础上，包含了如光照、模型、材质、摄像机等高级引擎特性，这些组件都是面向对象的，你可以用熟悉的 Objective-C 或 Swift 语言来编写代码。假如你用过 OpenGL 最早的版本，那时还没有 shader，只能苦逼的使用各种底层受限制的 API 开发。而 Scene Kit 就好了很多，对于大多数需求 (甚至像动态阴影和景深这种高级特性)，使用它提供的上层 API 来配置，就已经足够了。

不仅如此，Scene Kit 还允许你直接调用底层 API，或自己写 shader 进行手动渲染 (GLSL)。
#### 节点(SCNode)
不仅是光照、模型、材质、摄像机这几个具体的对象，Scene Kit 使用节点 (在3D图形学中，像这样的树状节点结构一般被称做 scene graph，这也是 Scene Kit 名称由来的一种解释) 以树状结构来组织内容，每个节点都存储了相对其父节点的位移、旋转角度、缩放等信息，父节点也是如此，一直向上，直到根节点。假如要给一个节点确定一个位置，就必须将它挂载到节点树中的某个节点上，可以使用下面的几个操作方法：
-addChildNode(_:)
-insertChildNode(_: atIndex:)
-removeFromParentNode()
这些方法与 iOS 和 OS X 中管理 view 和 layer 层级方法如出一辙。
![img](http://7xrxzy.com1.z0.glb.clouddn.com/1415779251380250.png)
#### 摄像机（SCNCamera）
![img](http://7xrxzy.com1.z0.glb.clouddn.com/3d_coordinate_system_2x.png)
#### 光照（SCNLight）
Scene Kit 中完全都是动态光照，使用起来一般会很简单，但也意味着与完整的游戏引擎相比，光照这块进步并不明显。Scene Kit 提供四种类型的光照：环境光、定向光源、点光源和聚光灯。

通常来说，旋转坐标轴和变换角度并不是设定光照的最佳方法。下面的例子表示一个光照对象通过一个节点对象来设置空间坐标，再通过 "look at" 约束，将光照对象约束到了目标对象上，即使它移动，光照也会一直朝向目标对象。

```bash

	let spot = SCNLight()
	spot.type = SCNLightTypeSpot
	spot.castsShadow = true
	
	let spotNode = SCNNode()
	spotNode.light = spot
	spotNode.position = SCNVector3(x: 4, y: 7, z: 6)
	
	let lookAt = SCNLookAtConstraint(target: knight)
	spotNode.constraints = [lookAt]
```
![gif](http://7xrxzy.com1.z0.glb.clouddn.com/spinning.gif)
#### 几何模型对象
Scene Kit 内建了几种简单的几何模型，如盒子、球体、平面、圆锥体等，但对于游戏来说，一般都会从文件中加载3D模型。你可以通过制定文件名来导入 (或导出) COLLADA 格式的模型文件：

```bash
	let chessPieces = SCNScene(named: "chess pieces") // SCNScene?
```
如果一个从文件里加载的场景可以全部显示时，将其设置成 SCNView 的 scene 就好了；但如果加载的场景文件中包含了多个对象，只有一部分对象要显示在屏幕上时，就可以通过名字找到这个对象，再手动加载到 view 上：

```bash

	if let knight = chessPieces.rootNode.childNodeWithName("Knight", recursively: true) {
    sceneView.scene?.rootNode.addChildNode(knight)
	}
```
这是一个对导入文件原始节点的引用，其中包含了任一和每一个子节点，也包括了模型对象 (包括其材质)，光照，以及绑定在这些节点上的摄像机。只要传入的名字一样，不论调用多少次，返回的都是对同一个对象的引用。
![img](http://7xrxzy.com1.z0.glb.clouddn.com/textures.png)
#### 动画
Scene Kit 的对象中绝大多数属性都是可以进行动画的，就像 Cocoa (或 Cocoa Touch) 框架一样，你可以创建一个 CAAnimation 对象，并指定一个 key path (甚至可以 "position.x") ，然后向一个对象施加这个动画。同样的，你可以在 SCNTransaction 的 "begin" 和 "commit" 调用间去改变值，和刚才的 CAAnimation 非常相似：

```bash

	let move = CABasicAnimation(keyPath: "position.x")
	move.byValue  = 10
	move.duration = 1.0
	knight.addAnimation(move, forKey: "slide right")
```
Scene Kit 也提供了像 Sprite Kit 那样的 action 形式的动画 API，你可以创建串行的动画组，也支持自定义 action 来协同使用。与 Core Animation 不同的是，这些 action 作为游戏循环的一部分执行，在每一帧都更新模型对象的值，而不只是更新表现层的节点。

## 实现虚拟现实界面与交互
实现虚拟现实的基本思想：模拟人的左右眼看到的图像细微差别，实现立体成像。在这个图像的基础上，加入身体移动与图像的交互，即低头抬头后转，移动位置后，图像会根据人体动作做出相应的改变，界面显示和真实世界展示相似。这样就实现了视觉上的虚拟现实。

未来虚拟现实还可以在听觉，触觉，甚至是味觉嗅觉等方面模拟现实的情况，实现更高级的虚拟现实，当然这是后话了。

我们这次要做的事情，就是在屏幕上创造出两个图像，对Camera在X轴上的位置进行细微调整，使两个场景的Camera模拟人的左右眼，创造视差，同时利用CoreMotion获取到的旋转角姿态角对Camera的Position进行调整，响应人体的动作。

#### Demo1Cardboard虚拟现实



