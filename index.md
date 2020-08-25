# Flutter iOS 混编
## 原生页面集成Flutter页面
### 一、创建Flutter模块

譬如你的工作目录在xxxx/Code/MyApp，那么你需要在xxxx/Code下执行，这样Flutter可以单独交给一个虚拟团队维护，各自独立。
```
$ cd xxxx/Code
$ flutter create -t module my_flutter
```

### 二、利用Cocoapods添加依赖
#### 1.在Podfile文件加入以下代码：
```
target 'xxxx' do

  #添加下面三行代码
  flutter_application_path = '../my_flutter'
  load File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')
  install_all_flutter_pods(flutter_application_path)

end

```

#### 2.执行pod install

注：如果报错
```
[!] Invalid Podfile file: No such file or directory @ rb_sysopen – ./my_flutter/.ios/Flutter/podhelper.rb.
```
需要在my_flutter文件夹下执行以下flutter run，把.ios和.android等flutter配置生成出来。

#### 3.禁用bitcode，设置环境变量
找到你的target，Build Settings->Build Options->Enable Bitcode 设置为NO，另外在Preprocessor Macros添加FLUTTER_BUILD_MODE=Debug和FLUTTER_BUILD_MODE=Release，如果还有其他环境需要根据情况选择Debug或Release。
![](https://www.goingta.cn/wp-content/uploads/2019/03/macros.jpg)

#### 4.添加run script

找到你的target，Build Phases -> + -> New Run Script Phase，并且在bash或zsh配置FLUTTER_ROOT，否则打包会出错找不到flutter。涉及多人开发还需要安装路径位置，否则团队小伙伴每个人的路径不一样。

```
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
```
注1：FLUTTER_ROOT为你得flutter SDK路径,若是报`xcode_backend.sh No such file or directory`错，解决方式参考：[https://github.com/flutter/flutter/issues/17234](url) 。若是个人项目，也可以直接填全路径把 $FLUTTER_ROOT替换成Flutte SDK路径。


注2：把这个script放到Target Dependencies phase script的后面。

注3：若是报
`Command PhaseScriptExecution failed with a nonzero exit code`错误。请把`Run script only when installing`勾选上


#### 5.愉快的写代码了
导入头文件`#import <Flutter/FlutterViewController.h>`
```
-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    FlutterViewController *fff = [FlutterViewController new];

    [self presentViewController:fff animated:YES completion:NULL];
}
```


参考:

[https://www.goingta.cn/178/](url)
