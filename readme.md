# canvas實用庫Fabric.js入門

## 什么是[Fabric.js](https://github.com/fabricjs/fabric.js "Fabric")？
让用户可以更方便使用HTML5 Canvas 元素的js庫，提供canvas缺少的对象模型，简化canvas编写，同時也是SVG-to-canvas解析器。开源项目，在GitHub目前有17K Star

## Fabric.js能做的事情
+ 在Canvas上创建、填充图形（包括图片、文字、规则图形和复杂路径组成图形）
+ 组合图形（包括组合图形、图形文字、图片等）、圖形動畫、圖形操作
+ 生成JSON，SVG数据等

## 为什么要使用Fabric.js？
Canvas提供画布能力，但是Api不够友好，绘制复杂的图形成本非常高。Fabric.js應運而生，它主要就是用对象的方式去编写代码。以下只以實現一個簡易畫板進行介紹，更多api請查看官方文檔

## 基礎api

### 1、声明画布

```html
<canvas id="canvas" width="600" height="600"></canvas>
```

```javaScript
let canvas = new fabric.Canvas('canvas')
```

### 2、繪製直線、虛線
```javaScript
let line = new fabric.Line([10, 10, 100, 100], { // 數組為起点和终点的横纵坐标
　　fill: 'green', // 填充色
　　stroke: 'green', // 笔触颜色
　　strokeWidth: 2, // 笔触宽度
})
canvas.add(line)

// 虛線-在绘制直线的基础上添加属性strokeDashArray
let dottedLine = new fabric.Line([10, 10, 100, 100], {
    fill: 'green',
    stroke: 'green',
    strokeDashArray: [3, 1] // strokeDashArray[a,b] => 每隔a个像素空b个像素
});
canvas.add(line)
```

### 3、繪製圖形、導入svg
```javaScript
let canvas = new fabric.Canvas('canvas')

let circle = new fabric.Circle({
    // type: "circle", // 對象類型
    originX: "center", // 对象转换的水平原点(‘left’，’right’，’center’)
    originY: "center", // 对象转换的垂直原点(‘left’，’right’，’center’)
    left: 100, // 横坐标
    top: 100, // 纵坐标
    // width: 160, // 宽度
    // height: 160, // 高度
    fill: "yellow", // 对象的填充色
    // flipX: false,
    // flipY: false,
    // overlayFill: null,
    stroke: "rgb(255,0,0)", // 线颜色
    strokeWidth: 5, // 线宽度
    strokeDashArray: null, // 虛線數組
    scaleX: 1, // 水平方向缩放倍数
    scaleY: 1, // 垂直方向缩放倍数
    angle: 0, // 偏轉角度
    snapAngle: null, // 设置对象在旋转时锁定的角度。比如设置值为45则对象在旋转时只能以45度为一个单位
    opacity: 1, // 透明度
    selectable: true, // 对象是否可选中，false时无法选中
    hasControls: true, // 值为false时无法对对象进行旋转和拉伸
    hasBorders: true,
    hasRotatingPoint: true,
    lockRotation: false, // true时无法旋转对象
    lockMovementX: false, // true时对象无法水平移动
    lockMovementY: false,  // true时对象无法垂直移动
    transparentCorners: true,
    perPixelTargetFind: false,
    shadow: null,
    visible: true, // 是否可見
    radius: 80 // 半徑
})
canvas.add(circle)

let rect = new fabric.Rect({
    left: 300,
    top: 100,
    width: 200, 
    height: 100, 
    rx: 20, // 圆角半径（x轴方向）
    ry: 20, // 圆角半径（y轴方向）
    fill: '#FF4D4F',
    strokeWidth: 2,
    stroke: "#dedede"
})
canvas.add(rect)

let triangle = new fabric.Triangle({
    left: 100,
    top: 400,
    width: 200, 
    height: 300, 
    fill: '#FF4D4F',
    strokeWidth: 2,
    stroke: "#dedede"
})
canvas.add(triangle)

fabric.Image.fromURL('../img/logo.png', (oImg) => {
    oImg.scale(2)
    canvas.add(oImg)
})
```

### 4、插入文字
```javaScript
let text = new fabric.Text('hello world', { 
    left: 100,
    top: 100,
    fontSize: 30, // 大小
    fontWeight: 'bold', // 加粗字型
    fontFamily: 'Comic Sans' // 字體家族
})
canvas.add(text)
```

### 5、組合
```javaScript
let canvas = new fabric.Canvas('canvas')

fabric.Image.fromURL('../img/department.jpg', (oImg) => {
    let text = new fabric.Text('I love 591', { 
        left: oImg.width / 2,
        top: oImg.height / 2,
        originX: 'center',
        originY: 'center',
        fill: 'red',
        fontSize: 88, // 大小
        fontWeight: 'bold', // 加粗字型
        fontFamily: 'Comic Sans' // 字體家族
    })

    let group = new fabric.Group([oImg,text], {
        left: 50,
        top: 50
    })
    canvas.add(group)
})
```

### 6、序列化
```javaScript
    let canvas = new fabric.Canvas('canvas')
    let rect = new fabric.Rect({
        width: 100,
        height: 100,
        fill: 'red'
    })
    canvas.add(rect)

    // 序列化
    let json = JSON.stringify(canvas.toJSON())
    let svg = canvas.toSVG()
    let obj = canvas.toObject()
    console.log(json)
    console.log(svg)
    console.log(obj)

    let tmpCanvas = new fabric.Canvas()
    // 反序列化
    tmpCanvas.loadFromJSON(json)
    for (let k in tmpCanvas.getObjects()) {
       canvas.add(tmpCanvas._objects[k])
    }

    fabric.loadSVGFromString(svg, (objects, options) => {
        let obj = fabric.util.groupSVGElements(objects, options)
        canvas.add(obj).renderAll()
    })

    // fabric.loadSVGFromURL('url', (objects, options) => {
    //     let obj = fabric.util.groupSVGElements(objects, options)
    //     canvas.add(obj).renderAll()
    // })
```

## ie坑點

### ie11 在canvas添加svg時默認沒有大小，需手動設置大小才能顯示正常
```javaScript
fabric.Image.fromURL(imgSrc, function(img) {
    img.set('width', 50).set('height', 50)
    canvas.add(fimg)
})
```

### IE throws Security Error when calling toDataUrl on canvas(圖片存在跨域且IE不支持設置crossOrigin,通過ajax曲線救國)
```javaScript
var xhr = new XMLHttpRequest()
xhr.onload = function () {
    var url = URL.createObjectURL(this.response)
    var img = new Image()
    img.onload = function () {
        // ... canvas code ...
        // 图片用完后记得释放内存
        URL.revokeObjectURL(url)
    };
    img.src = url
}
xhr.open('GET', url, true)
xhr.responseType = 'blob'
xhr.send()
```

## 功能實踐
+ [格局圖製作](https://www.591.com.tw/home/house/patternDrawing "格局圖製作")

## 總結
### 使canvas開發更便捷高效，更多功能請各位大神自行探索