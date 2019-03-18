---
title: 解决bug描述
---
## bug#41396 电机转速可以嵌套变量和常量

## Root Cause(引起问题的原因)
可能是刚开始做的时候没有明确这一块不能嵌套其他的值
## Solution（使用的方案）
将现有blockly块的field字段修改为不可嵌套其他值的field
## Affected files （修改的文件清单）
group_block.jsx

group_block_service.js
## Test case （测试用例）
同测试

-----------

## bug#41950 设置读数窗口或颜色窗口后界面显示异常 

### Root Cause(引起问题的原因)
在blockly中弹窗现在是统一将头部隐藏，点击取消或者遮罩头部显示。发现没有对弹窗里面的事件做处理（弹窗里面的列表元素选中的时候默认也是关闭弹窗的，但是由于现在的弹窗机制一些历史原因没有做一些统一的设定，导致需要修改每一个弹窗里面的事件，并在相应的事件里面调用显示头部导航的接口）
### Solution（使用的方案）
统一在onRemove方法里面 执行 $bridge.call("global.toggleShowProjectTabbar", 1)
### Affected files （修改的文件清单）
jsx_manager.js
### Test case （测试用例）
同测试

## bug#42120 【blockly-andriod】事件-超声波传感器-与 障碍物的距离，修改超声距离后按撤回键显示代码

### Root Cause(引起问题的原因)
在代码里面存在一些冗余代码，导致写数据的时候报错
### Solution（使用的方案）
将冗余代码删除
### Affected files （修改的文件清单）
blocks/events.jsx
### Test case （测试用例）
同测试

## bug#41949 偶发进入遥控Blockly界面显示多余按钮 && bug#42037 偶发缺少显示读数窗口和颜色窗口程序块

### Root Cause(引起问题的原因)
工作区初始化和调用openBlockly回调方法的执行顺序问题
### Solution（使用的方案）
将工作区初始化操作等到回调成功之后再执行
### Affected files （修改的文件清单）
webpack.config.js
bridgeForNative.js
app.js
index.html
### Test case （测试用例）
同测试

bug#42104 【Android-Blockly】录制系统动作时舵机模式冲突或掉线响应异常 

### Root Cause(引起问题的原因)
在弹出录制动作框之前遗漏了角模式和轮模式的判断
### Solution（使用的方案）
加上判断
### Affected files （修改的文件清单）
engine/components/common/blue_tooth_hoc.jsx
### Test case （测试用例）
同测试

bug#42080 【Android-Blockly】录制系统动作流程先后顺序有误
### Root Cause(引起问题的原因)
代码执行相关顺序出现了问题
### Solution（使用的方案）
纠正执行顺序
### Affected files （修改的文件清单）
engine/components/motion/jsx_create_action.jsx
### Test case （测试用例）
同测试

bug#42091 【Android-Blockly】录制系统动作命名动作弹框显示异常 
### Root Cause(引起问题的原因)
当弹窗的时候没有将头部导航隐藏，所以导致头部覆盖了当前块
### Solution（使用的方案）
弹窗时将头部隐藏
### Affected files （修改的文件清单）
参考之前的弹窗隐藏头部实现方案
### Test case （测试用例）
同测试

bug#42071 【Android-设置】充电保护下录制动作界面使主控充电，没有弹窗提示
### Root Cause(引起问题的原因)
功能开发的时候遗漏了这一块
### Solution（使用的方案）
api端添加相应的充电保护接口，blockly端主动调用并在编辑的时候弹窗提醒
### Affected files （修改的文件清单）
bridgeForNative.js
### Test case （测试用例）
同测试


bug#41336 【blockly-andriod】事件-红外传感器与障碍物的模块在连接主控后需要自动恢复正常色 
### Root Cause(引起问题的原因)
在链接蓝牙后没有重新初始化workspace中的块
### Solution（使用的方案）
连接蓝牙并获取到相应的外设后，重新初始化workspace
### Affected files （修改的文件清单）
bridgeForNative.js
### Test case （测试用例）
同测试

bug#41235 【我的项目-逻辑编程】第一个文本需要撤销两次才能消失
### Root Cause(引起问题的原因)
由于在创建一个text block块的时候历史纪录中记录了两个事件（change 和 create），所以导致每次创建一个块都对应了两个操作。最终回退的时候需要撤销两次才能消失
### Solution（使用的方案）
1. 多行文本框的block块中将 init方法中的调用 this.setInputsInline(true)删除
2. block_extension.js 中将原有moveby删除(这里会记录一次操作)，使用自身属性来设置当前位置
    ```javascript
    newBlock.initSvg();
    newBlock
    .getSvgRoot()
    .setAttribute("transform", "translate(" + Math.round(data.left - 350) + ", " + Math.round(data.top) + ")");
    newBlock.render();
    // newBlock.moveBy(data.left - 350, data.top);
    // newBlock.initSvg();
    // newBlock.render();
    ```
### Affected files （修改的文件清单）
block_extension.js
acousto_optic.jsx
### Test case （测试用例）
同测试

bug#42532 【iOS-blockly】未连接蓝牙时点击读取传感器数据提示错误
### Root Cause(引起问题的原因)
没有将当前提示信息放入语言包内
### Solution（使用的方案）
在英文和中文语言内添加 变量 BLUETOOTH_NOT_CONNECTED 
### Affected files （修改的文件清单）
en.js
cn.js
block_infared_sensor_shower.jsx
jsx_create_action.jsx
### Test case （测试用例）

bug#42497 【iOS-blockly】变成逻辑界面显示没有全屏
### Root Cause(引起问题的原因)
ios端需要对webview做特殊处理
### Solution（使用的方案）
ios端对webview做特殊处理
blockly 模块调整一下左侧菜单的样式 默认多出 44px就好
### Affected files （修改的文件清单）
iphoneX.css
### Test case （测试用例）

bug#42572 【iOS-blockly】多分支模块，蓝牙断开再次连接后无法使用
### Root Cause(引起问题的原因)
遵循的是1.0版本的处理方式
### Solution（使用的方案）
统一换成2.0版本处理方案
### Affected files （修改的文件清单）
stat.jsx
### Test case （测试用例）

bug#41338 【blockly-andriod】blockly 断开与主控盒连接后还能选择外设id 
### Root Cause(引起问题的原因)
遵循的是1.0版本的处理方式
### Solution（使用的方案）
统一换成2.0版本处理方案:

1. 如果连线图中不包含当前模块的外设，则该模块处于禁用状态（与蓝牙是否连接无关）；

2. 点击禁用状态模块的参数时，弹出toast弹窗，文案“未连接【外设名称】”；

3. 每次连接蓝牙时，连接图会更新传感器的数量和ID，不更新舵机和电机的数量和ID；

4. 重新连接蓝牙后，连线图比对当前编辑区的模块类型，如果更新的连线图中包含当前编辑区已禁用模块的传感器，则重新启用该模块。

### Affected files （修改的文件清单）
blockly_utils.js
popup_show.js
jsx_manager.js
sensors.jsx
### Test case （测试用例）

bug#43047 拖动‘超声波灯ID，颜色红_绿_蓝_’到工作区，点击颜色，界面卡住，无法删除或添加新的blockly
### Root Cause(引起问题的原因)
初始化进入blockly块的时候没有更新workspace中block的状态
### Solution（使用的方案）
初始化的时候更新状态 在openblockly 调用 blocklyUtils.refreshBlock()
### Affected files （修改的文件清单）
bridgeForNative.js
### Test case （测试用例）

bug#43110 【Android-blockly】新建blockly列表会覆盖旧的blockly列表
### Root Cause(引起问题的原因)
1. 之前从遥控器进入blockly和普通模式下进入blockly toolbox 内容不一样，在处理这一块异常时一股脑的重新初始化了workspace,导致现在新建blockly列表时，wokrspace中的块不会变
### Solution（使用的方案）
1. 添加 toolbox update 方法，将toolbox 内容的更新单独提出到一个新方法中。
2. 同时在原来的app.js中添加 WorkspaceInit.initWorkSpace 初始化工程的任务从 openblockly 回调中提取出来
### Affected files （修改的文件清单）
bridgeForNative.js
app.js
workspace_init.js
### Test case （测试用例）
同测试

bug#42946 【iOS-blockly-文本】保存默认的文本未输入内容时再次打开显示异常
### Root Cause(引起问题的原因)
1. 程序逻辑错误
### Solution（使用的方案）
修正逻辑
### Affected files （修改的文件清单）
field_text_multify.js
### Test case （测试用例）
同测试

bug#42949 【iOS-blockly】长按添加文本框时，有时会跑到视角区域外
### Root Cause(引起问题的原因)
block位置计算有误
### Solution（使用的方案）
修正位置
### Affected files （修改的文件清单）
block_extension.js
block_extensions_new.js
field_text_muiltify.js
context_ment/index.jsx
utils/index.js
### Test case （测试用例）
同测试

bug#43274 【Android-blockly-事件】亮度传感器的python代码中“强度值”的数据没显示出来
### Root Cause(引起问题的原因)
获取值的变量名写错
### Solution（使用的方案）
更正变量名
### Affected files （修改的文件清单）
python/events.js
### Test case （测试用例）
同测试

fix:  bug#43040 【iOS-Blockly】颜色窗口值为空，应设置默认值
### Root Cause
需求理解有误
### Solution
重新审视需求
### Affected files
acousto_optic.jsx
### Test case 
同测试

bug#43304 【Android-blockly编程-事件】超声波传感器事件的python代码的“距离值”数据无显示
### Root Cause
变量赋值错误
### Solution
正确赋值变量
### Affected files
python/events.js
### Test case 
同测试

bug#43298 【Android-blockly-运动】舵机轮模式转Python代码规范问题
### Root Cause
现在的方案已经转成中文了
### Solution
使用默认的数字和字母替代
### Affected files
python/group_block.js
### Test case 
同测试

bug#43325 【Android-blockly-动作】录制动作名称长度超出20个字符
### Root Cause
原先的默认长度是30
### Solution
修改默认长度为20
### Affected files
jsx_input_xmlName.jsx
jsx_create_action.jsx
### Test case 
同测试

bug#43492 【Android-blockly-工作栏】在界面编辑新建文本时，点击文本框外，会取消编辑，不保存输入的文本
### Root Cause
默认的是点击浮层会关闭弹框
### Solution
修改为点击浮层不关闭弹窗
### Affected files
components/context_menu/text.jsx
### Test case 
同测试

bug#43595 【iOS-blockly-开始】红外传感器ID多次切换后，不会退出置灰状态，需重点两次才会退出置灰状态。
### Root Cause
和超声波传感器处理方式不一样
### Solution
保持和超声波处理方式一样
### Affected files
blocks/start/start.jsx
### Test case 
同测试

bug#43370 【Android-blockly-运动】blockly调用被修改的动作名称不能更新
### Root Cause
属于临时发现的需求
### Solution
在切换的时候调用一下相关接口，重新设置当前workspace中所有动作相关的值
### Affected files
bridgeForNative.js
### Test case 
同测试


bug#43374 【Android-blockly-运动】blockly调用被删除的动作仍然显示
### Root Cause
属于临时发现的需求
### Solution
在切换的时候调用一下相关接口，重新设置当前workspace中所有动作相关的值
### Affected files
bridgeForNative.js
### Test case 
同测试

bug#43495 【iOS-遥控器】长按工作区可弹出可操作弹窗且存在问题
### Root Cause
遗漏的操作
### Solution
在从遥控器切换到blockly时做一个判断，不显示右键菜单
### Affected files
block_extension.js
utils/index.js
### Test case 
同测试


bug#43512 【Android-Blockly】点击选择控件会触摸响应下方程序块的设置弹框
### Root Cause
事件冒泡导致
### Solution
阻止事件冒泡
### Affected files
value_display.jsx
popup_container.jsx
### Test case 
同测试

bug#43318 【Android-blockly-运动】录制动作帧时输入框排版问题
### Root Cause
样式问题
### Solution
修正样式
### Affected files
jsx_input_xmlName.jsx
### Test case 
同测试


bug#43488 【Android-blockly-工作栏】进入已保存的项目，点击撤销键，再拖出块，再点击撤销键，此时撤销键不能使用
### Root Cause
1. 在初始化完毕之时没有清空掉undostack中的历史记录
### Solution
初始化的时候清空undostack
### Affected files
block_extension.js
workspace_init.js
### Test case 
同测试

bug#43458 【Android-blockly-开始】拖出两个相同id的红外传感器，其中灰置的红外传感器不能撤销
### Root Cause
有程序报错
### Solution
处理出错的程序
### Affected files
blocksManager.js
### Test case 
同测试

bug#43384 【Android-blockly-运动】加入“停止电机”块后不能转Python代码
### Root Cause
没有写转pyhton的代码
### Solution
加上转python的代码
### Affected files
python/motion.js
### Test case 
同测试

bug#43416 【Android-blockly-事件】红外传感器块的python代码里数值无显示（声音传感器也是）
### Root Cause
相关参数获取错误
### Solution
正确获取参数
### Affected files
python/events.js
### Test case 
同测试


 bug#43188 【Android-blockly-开始】“点击运行开始”无转Python对应的代码
 bug#43208 【Android-blockly-开始】“开始栏”的block块都没有转Python代码（分支程序需要分页）		
 bug#43312 【Android-blockly-工具】未嵌套的程序也显示出对应的Python代码

 ### Root Cause
遗漏的内容
### Solution
按照测试需求添加对应的功能
### Affected files
block_extension.js
generator.js
blockly_extensions/index.js
python.jsx
python/start.js
### Test case 
同测试


bug#43470 【android-blockly-工作栏】撤销“手机/平板”块后重做会灰置
 ### Root Cause
忽略了撤销操作的逻辑
### Solution
将撤销操作考虑进去
### Affected files
start.jsx
### Test case 
同测试

bug#43465 【Android-blockly-工作栏】拖出触碰传感器程序块，修改参数后撤销至消失，再重做，一直处于默认参数。
 ### Root Cause
忽略了撤销操作的逻辑
### Solution
将撤销操作考虑进去
### Affected files
field_touch_sensor.js

### Test case 
同测试

bug#43376 【Android-blockly-运动】调用动作转Python时显示的是ID而没有显示动作名称
 ### Root Cause
之前展示出来的是id
### Solution
修改为id对应的text
### Affected files
ptyhon/motion.js
### Test case 
同测试

bug#43480 【Android-blockly-数学】加减运算转Python未加括号
 ### Root Cause
原始代码没有添加括号
### Solution
将括号加上
### Affected files
ptyhon/math.js
### Test case 
同测试


bug#44516 【1.2.0.1-explore上传模式-逻辑】“跳出循环”的提示问题
 ### Root Cause
scratch-blocks 中警告框的外边框没有渲染出来
### Solution
在 scratch-blocks 中 inject.js中添加
```

var embossFilter = Blockly.utils.createSvgElement('filter',
  {'id': 'blocklyEmbossFilter' + rnd}, defs);
Blockly.utils.createSvgElement('feGaussianBlur',
  {'in': 'SourceAlpha', 'stdDeviation': 1, 'result': 'blur'}, embossFilter);
var feSpecularLighting = Blockly.utils.createSvgElement('feSpecularLighting',
  {
    'in': 'blur',
    'surfaceScale': 1,
    'specularConstant': 0.5,
    'specularExponent': 10,
    'lighting-color': 'white',
    'result': 'specOut'
  },
  embossFilter);
Blockly.utils.createSvgElement('fePointLight',
  {'x': -5000, 'y': -10000, 'z': 20000}, feSpecularLighting);
Blockly.utils.createSvgElement('feComposite',
  {
    'in': 'specOut',
    'in2': 'SourceAlpha',
    'operator': 'in',
    'result': 'specOut'
  }, embossFilter);
Blockly.utils.createSvgElement('feComposite',
  {
    'in': 'SourceGraphic',
        'in2': 'specOut',
    'operator': 'arithmetic',
    'k1': 0,
    'k2': 1,
    'k3': 1,
    'k4': 0
  }, embossFilter);
  options.embossFilterId = embossFilter.id;

```
### Affected files
scratcht-blocks/core/inject.js
### Test case 
同测试