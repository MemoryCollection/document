# AutoJs6 使用文档

原作者：https://github.com/SuperMonster003/AutoJs6-Documentation



## 常用函数

### 随机休息

~~~JavaScript
function sleepRandom(min = 1, max = 2) {
    if (arguments.length === 1) {
        max = min + 1;
        min = arguments[0];
    }
    var randomTime = Math.floor(Math.random() * (max - min + 1)) + min;
    sleep(randomTime * 1000);
}
// 测试示例1：默认1 - 2秒休息
sleepRandom();
// 测试示例2：传入一个参数，2 - 3秒休息
sleepRandom(2);
// 测试示例3：传入两个参数，3 - 5秒休息
sleepRandom(3, 5);
~~~

### 悬浮窗文字

~~~JavaScript
// 创建一个用于显示临时消息的悬浮窗
var logWindow = floaty.window(
    <frame gravity="center">
        <text id="logText" textSize="16sp" textColor="#f44336" backgroundColor="#00000080" padding="10dp"/>
    </frame>
);
// 定义类似log的函数
function logLike(message) {
    ui.run(() => {
        logWindow.logText.setText(message);
        setTimeout(() => {
            ui.run(() => {
                logWindow.logText.setText('');
            });
        }, 1000);
    });
}
// 测试调用
logLike('这是一条类似log的消息');
~~~

### 控件范围内随机点击

~~~JavaScript
/**
 * 在指定控件范围内随机点击，可设置边界限制和内边距
 * @param {UiObject} control - 已找到的控件
 * @param {Object} [limit] - 大范围边界限制，可选
 * @param {number} [limit.left] - 大范围左边界
 * @param {number} [limit.top] - 大范围上边界
 * @param {number} [limit.right] - 大范围右边界
 * @param {number} [limit.bottom] - 大范围下边界
 * @param {number} [padding = 10] - 控件内边距，防止点击到边框，默认 10 像素
 * @returns {boolean} - 如果成功点击返回 true，否则返回 false
 */
function clickRandom(control, limit, padding = 10) {
    // 检查是否传入有效的控件
    if (control) {
        // 获取控件的边界信息
        let bounds = control.bounds();
        // 获取控件的左、上、右、下边界坐标
        let left = bounds.left;
        let top = bounds.top;
        let right = bounds.right;
        let bottom = bounds.bottom;

        // 检查是否提供了边界限制
        if (limit && typeof limit.left!== 'undefined' && typeof limit.top!== 'undefined' && typeof limit.right!== 'undefined' && typeof limit.bottom!== 'undefined') {
            // 检查控件是否在指定的大范围内
            if (left < limit.left || top < limit.top || right > limit.right || bottom > limit.bottom) {
                return false; // 控件超出范围，不点击
            }
        }

        // 计算控件去除内边距后的可用范围
        let availableLeft = left + padding;
        let availableTop = top + padding;
        let availableRight = right - padding;
        let availableBottom = bottom - padding;

        // 确保可用范围是有效的
        if (availableLeft >= availableRight || availableTop >= availableBottom) {
            return false; // 内边距过大，没有可用范围，不点击
        }

        // 计算可用范围的宽度和高度
        let width = availableRight - availableLeft;
        let height = availableBottom - availableTop;

        // 在可用范围内随机生成一个点击的 X 坐标
        let randomX = availableLeft + Math.floor(Math.random() * width);
        // 在可用范围内随机生成一个点击的 Y 坐标
        let randomY = availableTop + Math.floor(Math.random() * height);

        // 使用 click 函数在随机生成的坐标处进行点击
        return click(randomX, randomY);
    }
    // 如果未传入有效的控件，返回 false
    return false;
}

// 示例使用
// 假设要查找 id 为 "bkk" 的控件
let editText = desc('抖音').findOne(800);

// 不设置边界限制，使用默认内边距
if (clickRandom(editText)) {
    console.log("成功在控件范围内随机点击");
} else {
    console.log("未找到符合条件的控件或点击失败");
}

// 设置边界限制和内边距
let limit = {
    left: 100,
    top: 200,
    right: 800,
    bottom: 600
};
let customPadding = 20;
if (clickRandom(editText, limit, customPadding)) {
    console.log("成功在控件范围内随机点击，且控件在指定范围内");
} else {
    console.log("未找到符合条件的控件、控件超出范围或点击失败");
}
~~~

## UI选择器
> UiSelector 对象是用于筛选活动窗口中控件节点的条件筛选器，可通过附加不同条件来筛选出一个或一组控件节点，并对其进行进一步处理。

### ui选择器类型

#### text：筛选包含指定文本的控件。

~~~JavaScript
text("立即开始");
~~~

#### desc：根据控件的内容描述进行筛选。

~~~JavaScript
desc("这是一个描述");
~~~

#### id：根据控件的 ID 进行筛选。

~~~JavaScript
id("com.example:id/button");
~~~

#### className：根据控件的类名进行筛选。

~~~JavaScript
className("android.widget.Button");
~~~

### 查找控件（单个）

~~~JavaScript
let w = text('抖音').findOne(800); 

if (w) {
    console.log(w.text()); 
}
~~~

### 查找控件（所有）

~~~JavaScript
// 构建选择器，筛选文本为 'hello' 
let sel = text('hello')
// 调用 find 方法筛选符合条件的控件集合
let wc = sel.find();
// 遍历控件集合并打印每个控件
wc.forEach(w => console.log(w));
~~~

### 控件粘贴文本

~~~JavaScript
let shuru = id("bkk").className("android.widget.EditText").findOne(800)
let editText =  id("bkk").findOne(800);
let success = shuru.setText("[害羞]");

let nodes = id("input").find();
// 将集合中所有控件的文本置为 "world"
nodes.setText("world");
~~~

### 控件前后滚动翻页

~~~JavaScript
// 控件向上滚动
let control = scrollable(true).findOne(800)
if (control) {
    if (control.scrollBackward()) {
        console.log("控件向后滚动成功");
    } else {
        console.log("控件向后滚动失败");
    }
}
// 控件向下滚动
let control = scrollable(true).findOne(800)
if (control) {
    if (control.scrollForward()) {
        console.log("控件向前滚动成功");
    } else {
        console.log("控件向前滚动失败");
    }
}
~~~
