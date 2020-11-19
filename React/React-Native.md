# React Native

```
React Native不只是手机端的UI框架，它同时封装和内置了很多调用手机硬件的API，如果使用react则需要完全借助原生包，而且在调用终端硬件功能上，react native可以完胜react
```

## 环境

### 安装依赖

Android Studio 是一个含有模拟器功能的编辑工具

虽然可以使用`任何编辑器`来开发应用（编写 js 代码），但仍必须安装 Android Studio 来获得编译 Android 应用所需的工具和环境

```
node				>=12版本
python2				不支持python3
jdk					必须为1.8版本 注意不要下jre！
Android Studio		
```

**配置java**

```
D:\java
D:\java\bin
D:\java\jre\bin
```

**配置Android Studio**

默认按了一个最新的sdk

react-native 需要Android9.0(Pie)版本（待考证）

配置后cmd输入adb有东西则

```
ANDROID_HOME
C:\Users\Marisue\AppData\Local\Android\Sdk	// 完成软件安装后文件默认放此路径

%ANDROID_HOME%\platform-tools	
```



----

### 手机连接

#### 真机

打开手机开发者模式-允许usb调试，允许usb安装文件，等待电脑配置手机成功，然后输入`adb devices`，若有内容，则说明配置成功

```markdown
C:\Users\MI\Desktop>adb devices
* daemon not running; starting now at tcp:5037
* daemon started successfully

List of devices attached
**di8t9ljzydvgnjts        unauthorized**
```

启动项目

启动时会新弹出另一个窗口，用于在8081端口启动一个叫做Metro Bundler的服务，这个窗口在开发时是需要保持运行着的

```
yarn android	// 不要用yarn start
```

在弹出cmd窗口中选择`To open developer menu press "d"`，此时会在手机上安装应用`app-native`，等待安装完成即可

如果不行可以配置端口再启动

```
adb reverse tcp:8081 tcp:8081
```

启动项目时需要在手机上下载的app，同时如果进入app内并且==摇晃手机==，可以弹出调试的相关设置

```
Reload				// 重刷整个应用，类似于在浏览器的F5刷新
Debug
Enable Live Reload
```



-----

#### 模拟器

 使用Android Studio 打开项目下的"android"目录，然后可以使用`AVD Manager`来查看可用的虚拟设备，在软件右上角右往左数第四个小图标（第一个是账户头像图标）

如果刚安装Android Studio则需要创建一个（需要下载）

然后点击启动加载模拟器

之后便可在运行项目（不需要在Android Studio下运行项目，例如vscode也能加载到该模拟器）



----

### 调试

**最基本的调试：**

打开手机app，摇晃弹出选项，选择Debug，电脑端自动弹出网页，直接在该网页下f12查看即可



**React Native Tools**



**vscode**

安装插件`React Native Tools`



-----

## 使用

```
react已有的东西不再补充
```

### 处理文本输入

`<TextInput/>`是一个允许用户输入文本的基础组件

属性：

- `onChangeText`：此属性接受一个函数，而此函数会在文本变化时被调用，参数为文本框的文本

- `onSubmitEditing`：在文本被提交后（回车键）调用

```jsx
import React, { Component } from 'react';
import { TextInput } from 'react-native';

export default function UselessTextInput() {
  const [valueState, onChangeTextFun] = React.useState('default text');  // 让函数式组件也能有state的Hooks
    
  return (
    <TextInput
      style={{ height: 40, borderColor: 'gray', borderWidth: 1 }}
      onChangeText={(text) => {    
        onChangeTextFun(text)     // 更改state里存放的文本
        alert(text)		  // 每修改一次文本就弹窗一次
      }}
      value={valueState}    // 更改显示的字体
      onSubmitEditing = {()=>{
        console.log('已提交:' + valueState)
      }} 
    />
    
  );
}
```



----

### 处理触摸事件