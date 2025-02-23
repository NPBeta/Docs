## 问题排查

本章节教你如何通过日志来排查遇到的各种问题（覆盖率100%）

日志文件的位置：`.minecraft/updater/updater.log`

使用任何文本编辑器打开日志文件（推荐使用等宽字体显示）

**如果你遇到无法按预期更新文件的问题**，请直接跳转到第5阶段`测试信息`进行查阅

---

日志分7部分，按顺序写在日志文件里：

1. 环境信息：`-------Environment Info------`
2. 配置信息：`-----Config File Content-----`
3. 首页信息：`-----Index Data-----`
4. 更新信息：`-----Update Data-----`
5. 测试信息：`-----Pattern Test------`
6. 修改信息：`-----File Modification List-----`
7. 下载过程：`-----Download Progress-----`

## 1.环境信息

日志开头就是环境相关的数据，比如工作目录，软硬件配置等数据

环境信息以`-------Environment Info------`开头

```yaml
# 程序的工作目录(CurrentWorkDirectory)
Workdir: D:/aprilforest/Desktop/updater/debug-directory

# 程序的版本
ApplicationVersion: 3.0.8

# 程序的启动参数（electron框架的process.argv变量）
process.argv: 
[
    "D:\\aprilforest\\Desktop\\updater\\node_modules\\electron\\dist\\electron.exe",
    "--trace-warnings",
    "./compiled/main/main.js"
]

# 程序的启动参数（electron框架的process.execArgv变量）
process.execArgv: 
[]

# 系统架构，一般是x64，话说应该没人用32位了吧
Architecture: x64

# 系统的平台，一般都是win32
Platform: win32

# 操作系统的名字，一般都是Windows_NT，别告诉我你这显示的是Ms-Dos
OpratingSystem: Windows_NT

# 操作系统的发行版本，10开头就是win10，win7的话，应该是7开头吧
OpratingSystemRelease: 10.0.19041

# 操作系统的版本，分为家庭版，专业版，或者其它版本
OpratingSystemVersion: Windows 10 Pro

# 物理内存使用情况，单位字节
Memory: 17379909632 / 25700540416

# 所有CPU
CPUs: 
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
{"model":"Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz"}
```

## 2.配置信息

配置信息以`-------Config File Content------`开头

```json
# 当前端调用了updaterApi.start()后，即开始更新时，就会输出这个信息
Start To Update

# -----Config File Content-----

# 此部分为配置文件'updater.yml'的内容，这里和'updater.yml'的内容完全一致
{
    "api": "http://127.0.0.1:800/index.php",
    "api1": "https://tetetet-1224063044.cos.ap-chengdu.myqcloud.com/index.yml",
    "postcalled_command1": "start https://www.mcbbs.net",
    "visible_time": 1500,
    "window_width1": 380,
    "window_height1": 130,
    "assets1": "index.html",
    "dev_tools": true
}
```

## 3.首页信息

首页信息就是更新服务器里的`index.yml`或者`config.yml`里的内容，两者完全一致。但静态服务端和php服务端有略有不同

```yaml
serverVersion: '3.0'
serverType: php
mode: common
paths:
  - .minecraft/mods
  - .minecraft/test

# 下面的部分是php服务端独有的，静态没有这两个字段
    # updateUrl： 这个地址用来获取校验文件的，以判断Minecraft客户端与更新服务器上文件的差异
    # updateSource： 这个地址用来实际下载缺失的文件的，一般指向res文件夹
updateUrl: http://127.0.0.1:800/index.php?purpose=update&source=res
updateSource: http://127.0.0.1:800/res/
```

## 4.更新信息

更新信息就是校验文件里的内容，这里输出了服务端有哪些文件，以及文件/目录间的结构

如果是静态服务端，那么就和`res.yml`文件里面的内容完全一致

如果是php服务端，并没有`res.yml`这个文件，因为这个文件是实时生成的，全程在内存里操作，并不会落盘

通常这个信息非常非常的长，我这里只截取了一小部分

```json
- name: .minecraft
  children:
    - name: 1.12.2-forge1.12.2-14.23.5.2855
      children:
        - name: '[照明优化]phosphor-forge-mc1.12.2-0.2.7-universal.jar'
          length: 10702
          hash: 031b1fde7df1dc99193baa7f30d09f5ecd711a71
        - name: '[耐久显示]+DurabilityViewer-1.12-forge14.21.1.2387-1.6.jar.jpg'
          length: 309795
          hash: 4d76b0e449ba71d61030253491f5a6220b6a839d
        - name: '[高清修复]OptiFine_1.12.2_HD_U_G5.jar'
          length: 208844
          hash: 375c131f8e61129c464da7b88e08fa0b4f64eb7b
    - name: mods
      children:
        - name: '[手柄支持]controllable-1.12.2-0.3.1.jar.disabled'
          length: 9480379
          hash: 1a45705f8ab45f7cc62ca81ec69492da4a36f2d8
        - name: '[潘马斯丰收工艺]Pam''s+HarvestCraft+1.12.2zd+Surprise+for+Yogcast.jar'
          length: 4888911
          hash: 7adcb4a37b36e8a76c6c36faecb2ad3255b81da3
        - name: '[神秘时代]Thaumcraft-1.12.2-6.1.BETA26.jar'
          length: 11349907
          hash: 04c94d22c486549918a78ec8595aec39ed2e02d9
```

## 5.测试信息

测试信息是最为关键的一部分。用来`测试`哪些文件匹配更新规则了，而哪些没有，甚至详细的匹配过程，都可以在此阶段找到，**特别适合用来定位无法更新文件的问题**！

此阶段应该是日志文件里最长的部分，我尽量用最简洁的语言说明

---

下面是一个栗子，栗子中用到的`客户端文件结构`：

```
.minecraft/
	updater/
		updater.log
		updater.yml
```

`服务端文件结构`

```
.minecraft/
    1.12.2-forge1.12.2-14.23.5.2855/
        照明优化.jar
        耐久显示.jpg
        高清修复.jar
    mods/
        手柄支持jar
        潘马斯丰收工艺.jar
        神秘时代.jar
```

`更新规则`也特别简单：

```yaml
mode: common
paths:
  .minecraft/mods
```

实际运行结果：

```
WorkMode: CommonMode

N: .minecraft (indirect)
N:     1.12.2-forge1.12.2-14.23.5.2855 ()
N:     mods (direct|indirect)
N:         手柄支持.jar (direct)
           Not found, download 手柄支持.jar
N:         潘马斯丰收工艺.jar (direct)
           Hash not matched: L: c8419053499d0a5f   R: 7adcb4a37be55c
N:         神秘时代.jar (direct)
           Not found, download 神秘时代.jar
-------------------
O: .minecraft (indirect)
O:     mods (direct)
O:         1.jar (direct)
           Delete: 1.jar
O:         潘马斯丰收工艺.jar (direct)
O:     updater ()
O: 启动器配置文件夹 ()
```

第二行的`WorkMode`是工作模式，这里只说`common`模式，`exist`模式因为过于简单，没有任何输出，暂不讨论

测试阶段的日志分两部分：

+ 上部分以`N:`开头，用来寻找需要`下载`的文件（`N`是`new`是首字母）
+ 下部分以`O:`开头，用来寻找需要`删除`的文件（`O`是`old`是首字母）

首先检查第一个目录`.minecraft`，因为`.minecraft`里还有2个子目录，所以也需要一并检查。

如果这个目录/文件能够被更新规则匹配，则会在括号里出现`direct`或者`indirect`字样，如果是空括号则表示不匹配。

+ `direct`表示能直接匹配更新规则（能够被更新规则直接命中），通常出现在`文件`后面
+ `indirect`表示自己本身不能匹配更新规则，但是有某个子目录却能命中更新规则，通常出现在`目录`后面

**小节：只要出现`direct`或者`indirect`里任意一个字样，就算命中了更新规则！**

如果你希望更新的文件没有这些字样，或者根本找不到你要更新的文件名，那么请检查更新规则是否填写正确，检查文件结构是否放置到指定的位置上，并与更新规则一致。

命中更新规则后，就会开始检查文件是否存在啦，校验是否匹配啦，如果不匹配的话，就会进入下载/删除任务列表。

上面的例子中：

+ `手柄支持.jar`这个文件，因为在本地找不到，所以会直接进入下载任务列表（直接下载新的文件）
+ `潘马斯丰收工艺.jar`这个文件，因为被修改了，所以本地校验和远程校验不一致，会同时进入删除列表和下载列表（意味先删除旧的，再下载新的）
+ `1.jar`这个文件，因为服务端不存在这个文件，所以客户端的`1.jar`是多余的，所以就会进入删除任务列表（直接删除旧文件）

## 6.修改信息

```
-----File Modification List-----
```

此阶段会列出所有需要下载和删除的文件名，以及路径等，如果发现和预期的行为不一致，请检查第5步`测试信息`阶段的输出内容
