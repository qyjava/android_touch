```
                  _           _     _   _                   _
  __ _ _ __   __| |_ __ ___ (_) __| | | |_ ___  _   _  ___| |__
 / _` | '_ \ / _` | '__/ _ \| |/ _` | | __/ _ \| | | |/ __| '_ \
| (_| | | | | (_| | | | (_) | | (_| | | || (_) | |_| | (__| | | |
 \__,_|_| |_|\__,_|_|  \___/|_|\__,_|  \__\___/ \__,_|\___|_| |_|
```

![android_touch](android_touch.gif)

## android_touch
android_touch 是一个向安卓设备发送多点触控事件的工具。通常，各种自动化脚本使用它在真实的 android 设备上发送触摸事件。

    android_touch是一个无依赖的本地android工具，用于发送多点触摸
    以非常高的速度和非常低的延迟输入。

android_touch 提供了一个 http 服务器，用于在 Android 设备上触发多点触控事件和手势。如果通过 ADB 启动，它无需 root 即可工作。触摸命令作为带有 JSON 数据的 http 请求发送到设备。

多点触控数据以 JOSN 形式发送，其中可以包含任何类型的触控事件，例如点击或复杂手势。

Android touch 是基于 libevdev 构建的，用于与触摸输入设备进行通信。

## 该如何使用它？

#### 设置设备
android_touch 的所有预构建可执行二进制文件都可以位于“libs”目录中。您需要首先确定您的 Android CPU 架构是什么，它可以是以下之一：
1. armeabi
2. armeabi-v7a
3. arm64-v8a
4. x86
5. x86_64
6. mips
7. mips64

确定 CPU 架构后，需要将“libs/{CPU_ARCH}/touch”推送到设备“/data/local/tmp”目录。

例如，如果您的 CPU 架构是“arm64-v8a”，则运行以下命令：
```bash
$ adb push libs/arm64-v8a/touch /data/local/tmp
```

#### 在设备上盯着 android_touch http 服务器

要在 android 设备上启动 android_touch http 服务器，请运行以下命令：
```bash
$ adb shell /data/loal/tmp/touch
``` 
这将在端口 9889 上启动 android_touch http 服务器

#### 将 android_touch http 端口转发到 localhost

由于 http 服务器运行在 Android 设备本身上，要从您的主机发送请求，您需要将 android 的端口 9889 转发到主机上的任何端口。例如，如果您想在主机 9889 端口上发送 http 请求：
 
```bash
$ adb forward tcp:9889 tcp:9889
``` 

#### 发送请求

发送 http 请求很简单，您可以使用任何编程语言并将 http 请求发送到 android_touch 服务器。例如在 python 中你可以使用 urllib2 来发送 http 请求，或者在 bash 中你可以使用 curl 工具来做同样的事情。

下面是使用 curl 工具在坐标 100x100 上发送点击触摸事件的示例：

```bash
$ curl -d '[{"type":"down", "contact":0, "x": 100, "y": 100, "pressure": 50}, {"type": "commit"}, {"type": "up", "contact": 0}, {"type": "commit"}]' http://localhost:9889
```

#### 了解多点触控 JSON 数据和格式

android_touch 允许您一次性向设备发送大量触摸命令。您可以通过发出单独的 http 请求来发送单个触摸命令，也可以打包所有触摸命令并一次性发送。一次性发送所有触摸数据显然会减少延迟。

android_touch http 服务器只接受命令对象的 json 数组。以下是触摸命令列表：

<table>
  <tbody>
    <tr>
      <th>命令</th>
      <th>描述</th>
    </tr>
    <tr>
      <td><h4>按下</h4></td>
      <td>
        <p>这会向 android 设备发送一个触摸事件以进行下一次提交，以下是必需的参数</p>
        <table>
          <tr>
            <td><b>contact</b></td>
            <td>
              这指定了触摸联系人 ID，联系人 ID 可以是 0 到 N-1，其中 N 是该设备上支持的最大触摸联系人数。在 android 设备上 N 一般为 5 到 10，即 5 个手指或 10 个手指触摸表面。
            </td>
          </tr>
          <tr>
            <td><b>x</b></td>
            <td>
              设备屏幕空间上触摸事件的 X 坐标。
            </td>
          </tr>
          <tr>
            <td><b>y</b></td>
            <td>
             设备屏幕空间上触摸事件的 Y 坐标。
            </td>
          </tr>
          <tr>
            <td><b>pressure</b></td>
            <td>
              按下事件的压力，如果不需要压力敏感事件，这可以是任何值。
            </td>
          </tr>
        </table>
      </td>
    </tr>
    <tr>
      <td><h4>滑动</h4></td>
      <td>
        这会将触摸移动事件发送到 android 设备以进行下一次提交，以下是必需的参数
        <table>
          <tr>
            <td><b>contact</b></td>
            <td>
             这指定了触摸联系人 ID，联系人 ID 可以是 0 到 N-1，其中 N 是该设备上支持的最大触摸联系人数。在 android 设备上 N 一般为 5 到 10，即 5 个手指或 10 个手指触摸表面。
            </td>
          </tr>
          <tr>
            <td><b>x</b></td>
            <td>
              设备屏幕空间上触摸事件的 X 坐标。
            </td>
          </tr>
          <tr>
            <td><b>y</b></td>
            <td>
              设备屏幕空间上触摸事件的 Y 坐标。
            </td>
          </tr>
          <tr>
            <td><b>pressure</b></td>
            <td>
              触摸移动事件的压力，如果不需要压力敏感事件，这可以是任何值。
            </td>
          </tr>
        </table>
      </td>
    </tr>
    <tr>
      <td><h4>抬起</h4></td>
      <td>
        这会向 android 设备发送一个修饰事件以进行下一次提交，以下是必需的参数
        <table>
          <tr>
            <td><b>contact</b></td>
            <td>
              这指定了触摸联系人 ID，联系人 ID 可以是 0 到 N-1，其中 N 是该设备上支持的最大触摸联系人数。在 android 设备上 N 一般为 5 到 10，即 5 个手指或 10 个手指触摸表面。
            </td>
          </tr>
        </table>
      </td>
    </tr>
    <tr>
      <td><h4>提交</h4></td>
      <td>
        这会向 android 设备发送提交命令，在发送提交命令之前，之前使用命令（例如down、up和move ）对触摸联系人所做的所有更改都不会在设备上可见。另请注意，在一次提交中，同一个联系人不能有多个向下、移动或向上。
      </td>
    </tr>
    <tr>
      <td><h4>延迟</h4></td>
      <td>
        这允许在触摸事件之间暂停，以下是必需的参数
        <table>
          <tr>
            <td><b>值</b></td>
            <td>
              以毫秒为单位的等待时间。
            </td>
          </tr>
        </table>
      </td>
    </tr>
  </tbody>
</table>

以下是上述命令的一些有效 json 对象：

<table>
  <tr>
    <th>Command</th>
    <th>Example Object</th>
  </tr>
  <tr>
    <td>down</td>
    <td>{"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50}</td>
  </tr>
  <tr>
    <td>move</td>
    <td>{"type": "move", "contact": 0, "x": 200, "y": 200, "pressure": 50}</td>
  </tr>
  <tr>
    <td>up</td>
    <td>{"type": "up", "contact": 0}</td>
  </tr>
  <tr>
    <td>commit</td>
    <td>{"type": "commit"}</td>
  </tr>
  <tr>
    <td>delay</td>
    <td>{"type": "delay", "value": 500}</td>
  </tr>
</table>

#### 例子

##### 单击一次

以 50 压力点击坐标 100x100

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "commit"},
  {"type": "up", "contact": 0},
  {"type": "commit"}
]
```
##### 双击

双击坐标 100x100，压力为 50，轻击之间有 100 毫秒延迟，同时释放。

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "commit"},
  {"type": "up", "contact": 0},
  {"type": "commit"},
  {"type": "delay", "value": 100},
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "commit"},
  {"type": "up", "contact": 0},
  {"type": "commit"}
]
```

##### 两指轻敲

同时点击两个坐标100x100和200x200，100ms后同时释放

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "down", "contact": 1, "x": 200, "y": 200, "pressure": 50},
  {"type": "commit"},
  {"type": "delay", "value": 100},
  {"type": "up", "contact": 0},
  {"type": "up", "contact": 1},
  {"type": "commit"}
]
```

同时点击两个坐标100x100和200x200，100ms后松开手指1，200ms后松开手指2

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "down", "contact": 1, "x": 200, "y": 200, "pressure": 50},
  {"type": "commit"},
  {"type": "delay", "value": 100},
  {"type": "up", "contact": 0},
  {"type": "commit"},
  {"type": "delay", "value": 100},
  {"type": "up", "contact": 1},
  {"type": "commit"}
]
```

##### 滑动

在 100x100 和 400x100 之间滑动

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "commit"},
  {"type": "move", "contact": 0, "x": 100, "y": 400, "pressure": 50},
  {"type": "commit"},
  {"type": "up", "contact": 0},
  {"type": "commit"}
]
```

滑动手势包括点 100x100、110x110、120x130、120x150

```json
[
  {"type": "down", "contact": 0, "x": 100, "y": 100, "pressure": 50},
  {"type": "commit"},
  {"type": "move", "contact": 0, "x": 110, "y": 110, "pressure": 50},
  {"type": "commit"},
  {"type": "move", "contact": 0, "x": 120, "y": 130, "pressure": 50},
  {"type": "commit"},
  {"type": "move", "contact": 0, "x": 120, "y": 150, "pressure": 50},
  {"type": "commit"},
  {"type": "up", "contact": 0},
  {"type": "commit"}
]
```

##### 复杂的手势

请参阅用于复杂手势生成的示例 python 脚本，该脚本生成以下手势：

![example](example/example.gif)

## 如何在本地构建？
android_touch 可以为 linux 和 android 平台构建。Linux 构建可以使用 cmake 和 make 完成，而 Android 构建可以使用 ndk-build 完成。

Linux 可执行文件可与 linux 多点触控输入设备驱动程序一起使用，其中 Android 可执行文件可用于包含多点触控输入触摸屏的 android 设备。

###  Android 构建

```bash
android_touch$ ndk-build
```

###  Linux 构建

```bash
android_touch$ mkdir build
android_touch$ cd build
android_touch/build$ cmake ..
android_touch/build$ make
```

## 如何调试事件？

如果您在 Android 上运行，则所有详细日志都将发送到 logcat，您可以通过以下方式查看内部工作：

```bash
$ adb logcat | grep touch
```

例如，以下是 logcat 中以下命令的调试输出：

```bash
$ curl -d '[{"type":"down", "contact":0, "x": 100, "y": 100, "pressure": 50}, {"type": "commit"}, {"type": "up", "contact": 0}, {"type": "commit"}]' http://localhost:9889
```

```text
android_touch: TouchInput : down : 0 : 100 : 100 : 50
android_touch: TouchInput : writeInputEvent : 3 : 47 : 0
android_touch: TouchInput : writeInputEvent : 3 : 57 : 1
android_touch: TouchInput : writeInputEvent : 3 : 48 : 6
android_touch: TouchInput : writeInputEvent : 3 : 50 : 4
android_touch: TouchInput : writeInputEvent : 3 : 53 : 100
android_touch: TouchInput : writeInputEvent : 3 : 54 : 100
android_touch: TouchInput : writeInputEvent : 0 : 0 : 0
android_touch: TouchInput : up : 0
android_touch: TouchInput : writeInputEvent : 3 : 47 : 0
android_touch: TouchInput : writeInputEvent : 3 : 57 : -1
android_touch: TouchInput : writeInputEvent : 0 : 0 : 0
```
