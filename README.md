# xiaolu-signature
用贝塞尔曲线编写签名板，实现平滑的笔条，无菱角
## 现在很多产品需求都需要用到电子签名

首先想到的解决方案是canvas画板，通过点击，移动事件，来获取滑动的坐标，再用canvas描线，就能达到画板写字的效果。

## 1、最简单的实现方法就是用以下的接口

```javascript
this.ctx.moveTo();
this.ctx.lineTo()
```
来看看效果
PC端：线条看起来有点不平滑...电脑上看还是不太明显
 <img src="https://img-blog.csdnimg.cn/2020122514201524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMwODQzNg==,size_16,color_FFFFFF,t_70"   width="50%">

手机端：可以看出很明显的菱角，线条不平滑；

 <img src="https://img-blog.csdnimg.cn/20201225142547390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMwODQzNg==,size_16,color_FFFFFF,t_70"   width="50%">
 
**原因很简单，moveTo和lineTo是通过俩个点连线来绘制线条的，注意是直线，所以我们看到绘制出来的线条是由一条一条直线连接在一起的**

## **2.解决方案**：二次贝塞尔曲线
![图片来自网络](https://img-blog.csdnimg.cn/20201225143216384.gif#pic_center)

二次贝塞尔曲线公式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201225140700142.png)
		通过公式可知，绘制一条贝塞尔曲线需要知道三个点，起点，终点，控制点，B(t)是曲线上的任意一点，t为常量；
		起点和终点我们可以轻松获取，但是控制点如何获取。所以我想到一个办法是，取路径上的三点绘制一条贝塞尔曲线，分别为P0,P1,P2，其中P0和P2分别为起点和终点,P1为曲线上经过的点，也就是公式上的B（t）；
```javascript
 P0（x1,y1）,P2(x2,y2), Pc(cx,cy)起点，终点，控制点
```
通过代入公式，计算简化后得出控制点

```javascript
cx = (x - (Math.pow(1 - t, 2) * x1) - Math.pow(t, 2) * x2) / (2 * t * (1 - t))
cy = (y - (Math.pow(1 - t, 2) * y1) - Math.pow(t, 2) * y2) / (2 * t * (1 - t))
t[0-1]
```
通过以上公式，直接代入坐标即可求得控制点；这就是我反向求二次贝塞尔曲线的方法；

用二次贝塞尔曲线绘制的线条，效果图
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020122514475597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMwODQzNg==,size_16,color_FFFFFF,t_70)
作者用以上方法写了一个uniapp的组件

组件地址：[点击跳转](https://ext.dcloud.net.cn/plugin?id=3757)
git地址：[git地址](https://github.com/LSZ579/xiaolu-signature.git)

目标，实现带笔锋的签名板，目前还有点问题，就是写太快不平滑
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201225152613279.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMwODQzNg==,size_16,color_FFFFFF,t_70#pic_center)
