---
tag: vr
title: CoreMotion虚拟现实和核心运动框架
---
本期分享虚拟现实技术其中之一，核心运动框架获取设备当前运动状态，实现与虚拟世界交互。

交互的实现
● 传感器 -- 核心运动框架(CoreMotion):
● 气压计,重力感应,陀螺仪
● 可以获取到的数据:
● 三轴重力加速度,三轴角速度,三轴用户给设备的加速度,三
轴磁力值;
● 气压及根据气压变化测量到的相对高度变化;
● 设备运动状态姿势; 
<!--more-->
## 虚拟现实
虚拟现实是通过设备捕捉人体的运动数据，运算后显示相应位置的图像与声音，营造出一种虚拟的环境，让人感到沉浸其中。
为了有这种沉浸感，就需要有设备捕捉到人体的运动数据，并根据数据判断位置，令图像和声音做出响应，实现与虚拟世界的交互。
想在iOS设备上开发一款VR软件测试学习使用，首先，要捕捉到手机的运动状态。所以我们需要学习相关的知识和内容。
## CoreMotion
iOS中获取和处理设备运动数据的框架就是CoreMotion[(官方文档)](https://developer.apple.com/library/ios/documentation/CoreMotion/Reference/CoreMotion_Reference/)
通过CoreMotion我们可以获取到设备当前的高度，重力加速度，加速度，姿态角等数据。通过这些数据我们就可以确定设备当前位置，从而根据位置显示相应的图像。实现人与虚拟世界的交互。

我们在项目Linked Frameworks and Libraries中引入CoreMotion.framework就可以开始使用CoreMotion框架了。
``` bash
#import <CoreMotion/CoreMotion.h>
```
### CMMotionManager
CoreMotionManager类能够使用到设备的所有移动数据(motion data)，CoreMotion框架提供了两种对motion数据的操作方式，一个是"pull"，另一个是"push"，其中"pull"方式能够以CoreMotionManager的只读方式获取当前任何传感器状态或是组合数据。"push"方式则是以块或者闭包的形式收集到你想要得到的数据并且会在特定周期内得到实时的更新。

CoreMotionManager为四种motion数据类型的每一个都提供了统一的接口：accelerometer,gyro,magnetometer和deviceMotion。

#### 设置更新频率

``` bash
[coreMotionManager setDeviceMotionUpdateInterval:1/60];//每秒刷新60次。
如果不设置更新频率，manager将以硬件能达到的最大刷新频率进行刷新
```

#### pull的方式获取数据：
for Example：
``` bash
#import "ViewController.h"
#import <CoreMotion/CoreMotion.h>

@interface ViewController ()
@end

@implementation ViewController{
    CMMotionManager *coreMotionManager;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    coreMotionManager = [[CMMotionManager alloc] init];
    [coreMotionManager setDeviceMotionUpdateInterval:1/60];
    [coreMotionManager startDeviceMotionUpdates];
}

-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event{
        NSLog(@"attitude.roll:%f",motion.attitude.roll);
        NSLog(@"attitude.yaw:%f",motion.attitude.yaw);
        NSLog(@"attitude.pitch:%f",motion.attitude.pitch);
        
        NSLog(@"rotationRate.x:%f",motion.rotationRate.x);
        NSLog(@"rotationRate.y:%f",motion.rotationRate.y);
        NSLog(@"rotationRate.z:%f",motion.rotationRate.z);
        
        NSLog(@"gravity.x:%f",motion.gravity.x);
        NSLog(@"gravity.y:%f",motion.gravity.y);
        NSLog(@"gravity.z:%f",motion.gravity.z);
        
        NSLog(@"userAcceleration.x:%f",motion.userAcceleration.x);
        NSLog(@"userAcceleration.y:%f",motion.userAcceleration.y);
        NSLog(@"userAcceleration.z:%f",motion.userAcceleration.z);
        
        NSLog(@"userAcceleration.x:%f",motion.magneticField.field.x);
        NSLog(@"userAcceleration.y:%f",motion.magneticField.field.y);
        NSLog(@"userAcceleration.z:%f",motion.magneticField.field.z);
}
@end
```

#### push的方式获取数据：
for Example：
``` bash
#import "ViewController.h"
#import <CoreMotion/CoreMotion.h>

@interface ViewController ()
@end

@implementation ViewController{
    CMMotionManager *coreMotionManager;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    coreMotionManager = [[CMMotionManager alloc] init];
    [coreMotionManager setDeviceMotionUpdateInterval:1/60];
    [coreMotionManager startDeviceMotionUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMDeviceMotion * _Nullable motion, NSError * _Nullable error) {
        NSLog(@"attitude.roll:%f",motion.attitude.roll);
        NSLog(@"attitude.yaw:%f",motion.attitude.yaw);
        NSLog(@"attitude.pitch:%f",motion.attitude.pitch);
        
        NSLog(@"rotationRate.x:%f",motion.rotationRate.x);
        NSLog(@"rotationRate.y:%f",motion.rotationRate.y);
        NSLog(@"rotationRate.z:%f",motion.rotationRate.z);
        
        NSLog(@"gravity.x:%f",motion.gravity.x);
        NSLog(@"gravity.y:%f",motion.gravity.y);
        NSLog(@"gravity.z:%f",motion.gravity.z);
        
        NSLog(@"userAcceleration.x:%f",motion.userAcceleration.x);
        NSLog(@"userAcceleration.y:%f",motion.userAcceleration.y);
        NSLog(@"userAcceleration.z:%f",motion.userAcceleration.z);
        
        NSLog(@"userAcceleration.x:%f",motion.magneticField.field.x);
        NSLog(@"userAcceleration.y:%f",motion.magneticField.field.y);
        NSLog(@"userAcceleration.z:%f",motion.magneticField.field.z);
    }];
}
@end
```

#### 参考坐标系
你可以设想一个根据设备某个方向来计算其他剩余角度的参考系，下面四中可用的参考系都假设设备平放在平面上，然后按照其指定的方向增加角度。

-(void)startDeviceMotionUpdatesUsingReferenceFrame:(CMAttitudeReferenceFrame)referenceFrame toQueue:(NSOperationQueue *)queue withHandler:(CMDeviceMotionHandler)handler NS_AVAILABLE(NA,5_0) __TVOS_PROHIBITED;
 
typedef NS_OPTIONS(NSUInteger, CMAttitudeReferenceFrame) __TVOS_PROHIBITED {
	CMAttitudeReferenceFrameXArbitraryZVertical = 1 << 0,
	CMAttitudeReferenceFrameXArbitraryCorrectedZVertical = 1 << 1,
	CMAttitudeReferenceFrameXMagneticNorthZVertical = 1 << 2,
	CMAttitudeReferenceFrameXTrueNorthZVertical = 1 << 3
};

CMAttitudeReferenceFrameXArbitraryZVertical 描述的参考系默认设备平放(垂直于Z轴)，在X轴上取任意值。实际上当你开始刚开始对设备进行motion更新的时候X轴就被固定了。

CMAttitudeReferenceFrameXArbitraryCorrectedZVertical 本质上和上一个一样，不过这里还使用了罗盘来对陀螺仪的测量数据做了误差修正，当然对于CPU来说会增加一定的消耗(对电池也一样)。

CMAttitudeReferenceFrameXMagneticNorthZVertical 同样是默认设备平放，然后X轴(也就是设备的右侧)指向地磁北向。这一设置需要在使用前用设备画"8"字来校正罗盘。

CMAttitudeReferenceFrameXTrueNorthZVertical 和上面一个一样，不过这里参考的是真实的地磁北极，因此会需要使用位置数据和和罗盘。


#### 获取气压和相对高度变化
for example：
``` bash
    altimeter = [[CMAltimeter alloc] init];
    if ([CMAltimeter isRelativeAltitudeAvailable]) {
        [altimeter startRelativeAltitudeUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMAltitudeData * _Nullable altitudeData, NSError * _Nullable error) {
            NSLog(@"%@",altitudeData.relativeAltitude);
            NSLog(@"%@",altitudeData.pressure);
        }];
    }
```


## 处理数据
到这里，我们已经可以通过CoreMotion获取到非常丰富的数据，这些数据具体代表什么，可以用他们做什么样的运算呢？

重力加速度(单位：G's)，加速度（单位：G's），角速度（单位：radians/second）磁力(单位：microteslas)
![img](http://7xrxzy.com1.z0.glb.clouddn.com/cmdm-axes.png)

VR 姿态角
![img](http://7xrxzy.com1.z0.glb.clouddn.com/vr-gear-8.jpg)

## 小结
目前我们已经可以获取到数据，并且了解到数据具体代表什么。
接下来就是用sceneKit构建一个虚拟的3D世界，根据用户的动作，改变视角，达到虚拟现实的视觉效果。
