### 添加右键打开

**Windows版**

新建一个文件，填入：

```
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\*\shell\VSCode]
@="Open with Code"
"Icon"="D:\\vocode\\Microsoft VS Code\\Code.exe"
 
[HKEY_CLASSES_ROOT\*\shell\VSCode\command]
@="\"D:\\vocode\\Microsoft VS Code\\Code.exe\" \"%1\""
 
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\Directory\shell\VSCode]
@="Open with Code"
"Icon"="D:\\vocode\\Microsoft VS Code\\Code.exe"
 
[HKEY_CLASSES_ROOT\Directory\shell\VSCode\command]
@="\"D:\\vocode\\Microsoft VS Code\\Code.exe\" \"%V\""
 
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode]
@="Open with Code"
"Icon"="D:\\vocode\\Microsoft VS Code\\Code.exe"
 
[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode\command]
@="\"D:\\vocode\\Microsoft VS Code\\Code.exe\" \"%V\""
```

```
路径改成安装vscode的位置即可
D:\\vocode\\Microsoft VS Code\\Code.exe （将这个全文替换掉即可）
```

然后保存后缀reg注册表文件，双击运行即可，然后就可把这个文件删掉了

----

### 快捷键

```
代码自动对齐
	Shift + Alt + F
代码缩一个水平格
	Shift + Tab
统一修改变量名
	光标左击选中某个变量，然后CTRL+Shift+L
```

