# android-so-loader

================================================================================

项目概述

================================================================================

核心功能是在PSVita上加载和运行Android游戏的原生SO库文件。

技术栈：
- SO文件加载：so_util库
  
- JNI环境模拟：FalsoJNI
  
- 图形适配：vitaGL (OpenGL ES → vitaGL)
  
- 控制映射：PSVita按键 → Android触摸事件
  
- 系统桥接：标准C库函数重新实现
  
================================================================================

================================================================================

android-so加载器开发流程
# 主要流程说明：

### 1.Windows安装wsl2的ubantu开发环境，越新越好！

[查看教程](https://zhuanlan.zhihu.com/p/475462241)

### 2.搭建vitasdk-soft版本（和正常的vitasdk一样的安装方法）

vitasdk-soft版本安装命令行
（1）
```bash
git clone https://github.com/vitasdk-softfp/vdpm
```
（2）
```bash
cd vdpm
```
（3）
```bash
./bootstrap-vitasdk.sh
```
（4）
使用文件查看器打开`.bashrc`文件，在最后面添加变量
```bash
export VITASDK=/usr/local/vitasdk # define $VITASDK if you haven't already
```
```bash
export PATH=$VITASDK/bin:$PATH # add vitasdk tool to $PATH if you haven't already
```
保存退出
记得以后每次登录ubantu记得在命令行输入以下代码：
```bash
source ~/.bashrc
```
（5）然后安装必要的库：
```bash
./install-all.sh
```
（6）安装完毕一定要再更新一下，不然后面各自缺运行库，你会很痛苦
在vdpm的目录下运行以下代码，他会自动更新到最新的所有库
```bash
./vitasdk-update
```
----------------------------
注意：

1.关于SceKernelDmacMgr_stub这个库，有些项目的cmake文件会把SceKernelDmacMgr（正确）写成SceKernelDmacmgr（错误）,注意手动更正cmake.TXT文件

[之前有人提过类似的问题](https://github.com/vitasdk/vdpm/issues/105)

2.关于sceLibcBridge报缺少的话，需要手动安装[克隆R神的此项目](https://github.com/Rinnegatamante/sceLibcBridge)！

具体操作：克隆后，使用make命令，会出现out文件夹，把里面的编译出的libSceLibcBridge_stub.a 和libSceLibcBridge_stub_weak.a 移动到~\usr\local\vitasdk\arm-vita-eabi\lib目录下方

```bash
cp out/libSceLibcBridge_stub.a $(VITASDK)/arm-vita-eabi/lib/
```

```bash
cp out/libSceLibcBridge_stub_weak.a $(VITASDK)/arm-vita-eabi/lib/
```
3.记得测试vitasdk-soft打包vpk是否正常，可以任意选个R神的自制游戏进行构建测试，例如[这个项目](https://github.com/Rinnegatamante/valiant-vita)
```bash
git clone https://github.com/Rinnegatamante/valiant-vita.git
```
```bash
cd valiant-vita
```
```bash
mkdir build && cd build
```
```bash
cmake .. && make
```
如果构建成功，那么你的开发环境就顺利搭建好了！！！

----------------------------------------------------

### 3.检测apk是否满足移植要求

移植安卓游戏要求：

✔️ 有ARMv6 或 ARMv7 可执行文件，只存在ARMv8 可执行文件就无法移植

✔️ 有OpenGL：要求为GLES 1 或 GLES 2，GLES 3+无法移植，低于的可以移植

✔️ FMOD：如果存在`libfmod.so`和`libfmodstudio.so`，可以移植声音，如果存在其他 FMOD 文件例如（`libfmodevent.so`，`libfmodex.so`），则游戏无法移植声音。


❌ Kotlin：如果存在 Kotlin 文件夹，但没有 lib 文件夹，则游戏无法移植。

❌ Unity 游戏：如果 lib 文件夹包含 libunity.so，则游戏无法移植。

❌ Java 游戏：如果 lib 文件夹包含 libgdx.so，则游戏无法移植。

判断OpenGL版本可以反编译AndroidManifest.xml，搜索glEsVersion数字（转换成二进制查看）

实在xml文件里面找不到的话可以考虑apk全部反编译，查看opengl调用的相关接口，例如我在《夏日口袋》这个官方安卓版了，在n.java这个文件发现的这一行
```bash
// 使用的是GL10接口，这表明：
import javax.microedition.khronos.opengles.GL10;
```
才发现这个游戏用的是gl10的接口，表明他属于OpenGL ES 1.1


[在线反编译apk-网站1](https://tool.tds.qq.com/apk-analyzer)
----------
补充说明（来自初代NPC大大）：

SoLoad加载器游戏移植说明

1游戏apk不是unity制作的，可以看其他公告鉴别

2游戏apk一定要有32位的so文件，如何鉴别就是看游戏apk里的lib文件夹里面有armeabi或armeabi-v7a文件夹就是32位的了。

3游戏apk要求openGL的版本为3.0及以下的

4游戏apk的游戏逻辑最好是在一个so文件里,因为现在好像只能加载一个so文件.

群里的soload加载器工具文件夹里有相应的工具和说明例子可以看

----------------------------------------------------

### 4.使用加载器推荐模板

```bash
git clone https://github.com/v-atamanenko/soloader-boilerplate.git
```

