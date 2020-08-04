# 设计稿
![image.png](http://cdn.airfunc.com/56c660bf3f314c1b82293ac94742823a)
# 实现
根据设计稿可以发现，整个组件的结构由一个等边六边形与一个矩形构成。构成的不规则图形在鼠标hover时出现外发光效果。
如果只是用一个等边六边形与一个矩形去实现，那么在鼠标hover时的效果将如下图所示：
![image.png](http://cdn.airfunc.com/fda32fd0e7974937befb58ef0f0621fb)
正六边形的外发光效果进入了矩形内,与设计稿不符。所以我们需要一个遮罩去当着进入到矩形范围内到发光效果，如下图所示：
![image.png](http://cdn.airfunc.com/4fe21efb47be4b24b7bc2bd10fa21832)
这又导致了我到等边六边形被矩形遮罩给挡住了，所以我们需要新加一层正六边形遮罩叠加在矩形遮罩之上。最终的结构如下：
![image.png](http://cdn.airfunc.com/7ef734fbf04b4d8b973996634ad8320c)
最下方为整个组件的容器，并且负责hover时的外发光效果；第二层为正六边形图形，负责外发光的显示。第三层为矩形的遮罩，负责遮挡住正六边形的外发光效果；第四层就是设计稿中所看到的正六边形。

## 六边形的实现
组件的整体框架结构已经有了，那么就剩下了正六边形如果绘制的问题。我们知道，html并没有为我们提供绘制多边形标签，所以我们需要开动小脑筋通过各种奇技淫巧来实现。
好在css3的产生，我们可以通过对矩形的旋转来实现等边六边形的绘制，如下图所示：
![image.png](http://cdn.airfunc.com/3ce9b4e2ce4042a2a2821317d3799fc7)
我们通过对3个矩形的不同角度旋转就可以实现：
1. 第一个矩形旋转0度
2. 第二个矩形旋转60度
3. 第三个矩形旋转-60度

效果如下：
![image.png](http://cdn.airfunc.com/7d6c9d8b1f3342468f816ccf2cbe44c7)
问题又来了，对矩形旋转后的结果并没有形成一个等边六边形，而是一个诡异的图形，原因是矩形的长宽比例。所以我们需要对矩形对长宽进行计算。如下图：
![image.png](http://cdn.airfunc.com/f2649f2a308347ebb7dddb2b0bb205b8)
一个等边六边形是由六个等边三角形组成。等边三角形每个角为60°。根据三角函数可以得到等边三角的高为sin60°L。那么组成等边六边形的矩形的高度为2sin60°L，即√3L≈ 1.732 * L。

以矩形宽度为60px为例，那么它的高度就 1.732*60 = 103.92。
得到如下代码：  
### html
```html
 <div class="panel__hexagon">
   <div class="panel__hexagon-left panel__hexagon-item"></div>
   <div class="panel__hexagon-center panel__hexagon-item"></div>
   <div class="panel__hexagon-right panel__hexagon-item"></div>
 </div>
```
### css
```css
    // 正六边形容器
    .panel__hexagon {
        width: 60px;
        height: 104px;
        position: relative;
    }

    // 正六边形 旋转的矩形
    .panel__hexagon-item {
        width: 100%;
        height: 100%;
        background: blue;
        position: absolute;
        top: 0;
        left: 0;
    }

    // 第一个旋转的矩形
    .panel__hexagon-right {
        transform: rotate(60deg);
    }

    // 第二个旋转的矩形
    .panel__hexagon-left {
        transform: rotate(-60deg);
    }
```



## 最终代码

### html
```html
	<!-- 容器 -->
        <div class="panel">
            <!-- 正六边形外发光 -->
            <div class="panel__hexagon panel__hexagon--shadow">
                <div class="panel__hexagon-left panel__hexagon-item"></div>
                <div class="panel__hexagon-center panel__hexagon-item"></div>
                <div class="panel__hexagon-right panel__hexagon-item"></div>
            </div>
            <!-- 矩形遮罩 -->
            <div class="panel__mark"></div>
            <!-- 正六边形遮罩 -->
            <div class="panel__hexagon">
                <div class="panel__hexagon-left panel__hexagon-item"></div>
                <div class="panel__hexagon-center panel__hexagon-item"></div>
                <div class="panel__hexagon-right panel__hexagon-item"></div>
            </div>
        </div>
```

### css
```css
    // 容器
    .panel {
        position: relative;
        width: 300px;
        height: 300px;
        margin: 0 100px;
    }

    // 遮罩
    .panel__mark {
        width: 300px;
        height: 300px;
        background: #fff;
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        z-index: 1;
        transition: box-shadow .6s;
    }

    // 正六边形容器
    .panel__hexagon {
        width: 60px;
        height: 104px;
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        top: -30px;
        z-index: 2;
    }

    // 正六边形 旋转的矩形
    .panel__hexagon-item {
        width: 100%;
        height: 100%;
        background: blue;
        position: absolute;
        top: 0;
        left: 0;
    }

    // 第一个旋转的矩形
    .panel__hexagon-right {
        transform: rotate(60deg);
    }

    // 第二个旋转的矩形
    .panel__hexagon-left {
        transform: rotate(-60deg);
    }

    // 正六边形 外发光容器
    .panel__hexagon--shadow {
        z-index: 0;
    }

    // 正六边形 外发光 旋转的矩形
    .panel__hexagon--shadow .panel__hexagon-item {
        background: transparent;
        transition: box-shadow .3s;
    }

    // hover 效果
    .panel:hover .panel__mark,
    .panel:hover .panel__hexagon--shadow .panel__hexagon-item {
        box-shadow: 0 0 15px rgba(0, 0, 0, .61)
    }
```


## 最终效果
![屏幕录制20200720 下午6.gif](http://cdn.airfunc.com/ecc1921961364852ad6d9a16fde59651)