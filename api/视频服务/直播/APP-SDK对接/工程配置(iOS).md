## 下载SDK

您可以在腾讯云官网下载移动端直播SDK

> [https://www.qcloud.com/doc/product/267/4821](https://www.qcloud.com/doc/product/267/4821)

解压SDK后，得到一个`TXRTMPSDK.framework`。目前RTMP SDK所有功能都集成在这一个framework中。

## Xcode工程设置

### 一、支持平台

+ SDK支持iOS 7.0以上系统

### 二、开发环境

+ Xcode 7或更高版本
+ OS X 10.10或更高版本

### 三、Xcode工程设置

下面通过一个简单的iOS Application工程，说明如和在Xcode工程中配置SDK。

### 1、拷贝SDK文件

在本例中，新建一个名字叫做HelloSDK的iOS工程，将下载下来的`TXRTMPSDK.framework`拷贝至工程目录。目录结构如下图所示：

![](//mccdn.qcloud.com/static/img/235308a7d33f2f8c921a048737899c24/image.png)

### 2、添加Framework

在工程中添加`TXRTMPSDK.framework`，同时还要添加以下系统依赖库

> 1. VideoToolbox.framework
> 2. SystemConfiguration.framework
> 3. CoreTelephony.framework
> 4. AVFoundation.framework
> 5. CoreMedia.framework
> 6. CoreGraphics.framework
> 7. libstdc++.tbd
> 8. lib.tbd
> 9. libiconv.tbd

所有添加完毕，工程依赖如下图所示：

![](//mccdn.qcloud.com/static/img/a9c75d5ba92382c1549f3a99317cbd1f/image.jpg)

关闭工程Bitcode选项。

![](//mccdn.qcloud.com/static/img/4298f90507a749625d7e92cc9004c1b1/image.png)

### 三、验证

下面在HelloSDK的代码中，调用SDK的接口，获取SDK版本信息，以验证工程设置是否正确。

### 1、引用头文件

在ViewController.m开头引用SDK的头文件：

```objective-c
#import "TXRTMPSDK/TXRTMPAPI.h"
```

### 2、添加调用代码

在viewDidLoad方法中添加代码：

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    // 打印SDK的版本信息
    NSLog(@"SDK Version = %@", [[TXRtmpApi getSDKVersion] componentsJoinedByString:@"."]);
}
```

### 3、编译运行

如果前面各个步骤都操作正确的话，HelloSDK工程应该可以顺利编译通过。在Debug模式下运行APP，Xcode的Console窗格会打印出SDK的版本信息。

> 2016-07-12 16:16:15.767 HelloSDK[17929:7488566] SDK Version = 1.4.1

至此，工程配置完成。