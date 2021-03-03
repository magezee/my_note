# React Native

```
React Native不只是手机端的UI框架，它同时封装和内置了很多调用手机硬件的API，如果使用react则需要完全借助原生包，而且在调用终端硬件功能上，react native可以完胜react
```

## 环境配置

### window

```
window一般只能开发android而不能开发ios
```

**安装环境**

`Android Studio` 是一个含有模拟器功能的编辑工具，虽然可以使用`任何编辑器`来开发应用（编写 js 代码），但仍必须安装 `Android Studio` 来获得编译 Android 应用所需的工具和环境

```
node				>=12版本
python2				不支持python3
jdk					必须为1.8版本 注意不要下jre！
Android Studio		
```

- 配置java

```
D:\java
D:\java\bin
D:\java\jre\bin
```

- 配置Android Studio

  默认按了一个最新的sdk

  react-native 需要Android9.0(Pie)版本（待考证）

  配置后cmd输入adb有东西则

```
ANDROID_HOME
C:\Users\Marisue\AppData\Local\Android\Sdk	// 完成软件安装后文件默认放此路径

%ANDROID_HOME%\platform-tools	
```



---

### mac

**ios**

电脑环境准备：[参考文章](https://www.jianshu.com/p/93c4cd8390d3)

1. 装 `CocoaPods` 简称 `pod` ——IOS项目上负责管理依赖的工具，即对第三方库的依赖。开发iOS项目不可避免地要使用第三方开源库，它可以节省设置和更新第三方开源库的时间

2. 要装 pod 需要装 `Homebrew` 简称 `brew` ——Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，相当于 Linux 的 `apt-get、yum`

   安装 `Homebrew`，直接在终端输入 ：

   ```markdown
   官方给的命令：
   	/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   下载太慢可以换国内源（参考https://zhuanlan.zhihu.com/p/111014448）：
   	/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
   ```

   在终端输入相关命令检查是否安装成功

   ```
   brew
   ```

3. 安装 `RVM` ——一个 `ruby` 版本管理以及安装工具

   ```
   curl -L https://get.rvm.io | bash -s stable
   ```

   查看是否安装成功

   ```
   rvm -v
   ```

4. 安装 `ruby`

   方式一：使用 RVM 安装：

   ```
   rvm install +指定版本				// 安装指定版本
   rvm install ruby --head		 // 安装最新版本
   ```

   方式二：使用 brew 安装：

   ```
   brew install ruby
   ```

   如果报没有指定目录的错误，则在访达查找终端并复制一个终端重命名 `Rosetta终端` 并且右键 `显示简介` 勾上 `使用Rosetta` 打开，然后使用此终端去输入命令下载即可

   查看是否安装成功

   ```
   ruby -v
   ```

5. 安装 `pod` 

   ```
   sudo gem install cocoapods
   ```

   查看是否安装成功

   ```
   pod
   ```

---

项目环境准备

1. 安装 node 包

   ```
   进入根目录
   yarn install
   ```

2. 安装和更新 pod 包

   ```
   进入ios目录	
   pod update
   ```

3. 启动服务

   ```
   进入根目录
   yarn start
   ```

4. 启动模拟器，启动 `xcode` 进入项目 `ios` 目录点击运行按钮，启动完后便会看到模拟器画面

   遇到的坑：

   - 报错：`atta-app/release_ios/main.jsbundle:No such file or directory`，解决：拿一个 `main.jsbundle` 文件放在项目指定目录即可

----

**android**

环境准备

1. 下载安装 `Android Studio`  [参考博客](https://blog.csdn.net/qq_38436214/article/details/106658550?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control)

2. 配置 `adb` 环境变量

   ```markdown
   vi ~/.bash_profile 然后在最后添加
   	#Setting PATH for Android ADB Tools
   	export ANDROID_HOME=/Users/gegeda/Library/Android/sdk
   	export PATH=${PATH}:${ANDROID_HOME}/platform-tools
   	export PATH=${PATH}:{ANDROID_HOME}/tools
   ```

3. 启动 `react-native` 项目

   ```
   项目根目录
   yarn start
   ```

4. 构建 `android` 项目

   使用 `android studio` 打开项目的 `android` 目录，自动构建

   因为要下载，国内被墙，因此要修改一下代理

   ```
   https://blog.csdn.net/qq_43553444/article/details/105596961		// 暂不行
   https://www.cnblogs.com/it-tsz/p/10295383.html								// 暂不不行
   ```

5. 启动 `android` 项目连接真机

   ```
   项目根目录
   yarn android
   ```



----

## 调试

```markdown
最基本的调试：
	打开手机app，摇晃弹出选项，选择Debug，电脑端自动弹出网页，直接在该网页下f12查看即可
vscode
	安装插件 React Native Tools
```

**真机**

```
一般使用真机或android中的模拟器来调试android项目
使用xcode中的模拟器来调试ios项目
```

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



---

**模拟器**

- android

  使用 `Android Studio` 打开项目下的 `android` 目录，然后可以使用`AVD Manager`来查看可用的虚拟设备，在软件右上角右往左数第四个小图标（第一个是账户头像图标），如果刚安装Android Studio则需要创建一个（需要下载），然后点击启动加载模拟器，之后便可在运行项目（不需要在Android Studio下运行项目，例如vscode也能加载到该模拟器）

- ios

  直接打开 `xcode` 打开项目 `ios` 目录运行项目便会自动打开模拟器



---

**网页**

像开发网页一样，如果在项目中想打印信息在浏览器上查看的话，需要在手机中 `ctrl+d` 打开调试菜单并开启 `debug` 模式

默认的项目会启动在 http://localhost:8081/debugger-ui/ 中进行调试（注意此地址的网页只能打开一个，否则会有错误）

 

---

## 初始化

```
https://reactnative.cn/docs/environment-setup
```

**脚手架构建初始项目**

```markdown
JS版本
	npx react-native init native_test
TS版本
	npx react-native init native_test --template react-native-template-typescript
```

项目中生成两个文件夹 `android` 和 `ios` 

<img src="https://img-blog.csdnimg.cn/20210119131318415.png" style="margin:0;width:300px">

```markdown
启动android
	yarn android
启动ios
	yarn ios
都是在项目根目录的位置去启动
```

启动项目会弹出一个新的终端显示项目相关情况，如果想重新执行启动项目需要把该终端关掉才能正常启动



-----

## 组件

### 布局组件

#### [View](https://www.react-native.cn/docs/view)

在 `react-native` 中，不可以像 `react` 那样使用原生HTML标签，如 `<div/>` 是没有办法通过编译的，所有最底层的标签都由 native 提供

View 是 native 中最基本的元素布局组件，相当于 `div` 标签，一般用于划分一块区域

---

#### [SafeAreaView](https://www.react-native.cn/docs/scrollview)

用于"带刘海"的手机屏幕，将全部的渲染内容放在不会被刘海遮住的地方（ios系统独有组件）

只需要包住渲染的全部组件就行

```jsx
<SafeAreaView>
	<View>
  	...
  </View>
</SafeAreaView>
```

---

#### [ScrollView](https://www.react-native.cn/docs/scrollview)

ScrollView 必须有一个确定的高度才能正常工作，因为它实际上所做的就是将一系列不确定高度的子组件装进一个确定高度的容器（通过滚动操作）

要给 ScrollView 一个确定的高度的话，要么直接给它设置高度（不建议），要么确定所有的父容器都有确定的高度

一般来说会给 ScrollView 设置 `flex: 1` 以使其自动填充父容器的空余空间，但前提条件是所有的父容器本身也设置了 flex 或者指定了高度，否则就会导致无法正常滚动

----

#### [TouchableHighlight](https://www.react-native.cn/docs/touchablehighlight)

用于封装视图组件，当按下的时候，封装的视图的不透明度会降低，同时会有一个底层的颜色透过而被用户看到，使得视图变暗或变亮

注意 `TouchableHighlight `只支持一个子节点（不能没有子节点也不能多于一个）如果包含多个子组件，可以用一个 View 来包装它们

```jsx
<TouchableHighlight>
	<View>
  	<Text>text</Text>
  </View>
</TouchableHighlight>
```

#### [TouchableOpacity](https://www.react-native.cn/docs/touchableopacity) 

和 `TouchableHighlight` 类似，封装的视图的不透明度会降低，但是不会有额外的颜色效果，泛用性更高

---





---

### 功能组件

#### [TextInput](https://www.react-native.cn/docs/textinput)

允许用户输入文本的基础组件

主要属性及方法：

- `onChangeText`：接受一个函数，而此函数会在文本变化时被调用，参数为文本框的文本

- `onSubmitEditing`：接受一个函数，在文本被提交后（回车键）调用

```jsx
import React from 'react';
import { TextInput } from 'react-native';

export default function UselessTextInput() {
  const [valueState, onChangeTextFun] = React.useState('default text');  
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



---

## 设计样式

### 规范

`react native` 中写样式不使用 `.css` 文件，而是 `.js` 或 `.ts` 并且借助框架中提供的方法 `StyleSheet.create` 来编写样式文件，或者直接写行内样式

```jsx
<view>
	<Text style={styles.textColor}>React Native</Text>
</view>

const styles = StyleSheet.create({
  textColor: {
    color: "#20232a",
    backgroundColor: "#61dafb"
  }
})
```

 需要注意的是，native 中的 css 单位写法有不同

写绝对单位时，默认单位是 `dp` 是手机端对 `px` 的一种换算

```js
const styles = StyleSheet.create({
  wrap: {
    padding: 40
  }
})
```

除了相对单位，写其他 css 值都需要用写成字符串格式

```js
const styles = StyleSheet.create({
  wrap: {
    padding: '40%'
    position: 'relative'
  }
})
```



---

### 适配单位

react-native 中不能使用 `vw` 这种相对屏幕的单位，取而代之的相对单位常用的只有 `%` ，如果想要根据屏幕宽高来定位元素，需要手动计算，即先拿到该设备的屏幕大小，然后手动去乘除特定值，来达到 `vw` 的功能

```js
import EStyleSheet from 'react-native-extended-stylesheet'
import { Dimensions } from 'react-native';

const { width,height } = Dimensions.get('window');		// 获取到屏幕的宽高

export default EStyleSheet.create({
  wrap: {
    width: width / 2,
    height: 2 * height,
  }
})
```

如使用 `ImageBackground` 来制作组件底图，必须要设置具体的长宽才能显示，这时就可以使用这种方法

```jsx
const { width,height } = Dimensions.get('window');

<ImageBackground 
	source={...},
  resizeMode='cover'			// 制作底图一般这个选cover
  style={{
    width: width,
    height: 3.7 * width		// 宽度等于屏幕宽度，高度的具体值需要慢慢调，这个值实际上就是图片的高/宽的比例
  }}
/>
```







---

## 设计路由

使用 `react-navigation` 来构建路由跳转

**安装**

安装包

```
yarn add @react-navigation/native
```

安装依存关系到 expo，如果没有 expo 则 `npm install -g expo-cli`

```
expo install react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

安装依赖包到项目

```
yarn add react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

如果是开发 ios 版本，还需要安装 pod 完成链接

```
npx pod-install ios
```

----

**使用**

React Navigation带有三种默认的navigator：

- `StackNavigator` —— 为应用程序提供了一种页面切换的方法，每次切换时，新的页面会放置在堆栈的顶部
- `TabNavigator` —— 用于设置具有多个Tab页的页面，用于在几个页面中自由切换，如微信底栏
- `DrawerNavigator` —— 用于设置抽屉导航的页面

