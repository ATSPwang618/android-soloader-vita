# android-so-loader
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

待续！！！

----------------------------------------------------

### 4.使用加载器推荐模板

```bash
git clone https://github.com/v-atamanenko/soloader-boilerplate.git
```

