title: 贝塞尔曲线学习笔记
date: 2015-04-08 12:25:28
categories:  [读书笔记]
tags:  [贝塞尔曲线,计算机图形,数学]
---
&emsp;&emsp;贝塞尔曲线(Bezier Curve)是由法国工程师皮埃尔·贝塞尔(Pierre Bezier)于1962年提出的一种曲线。在数学的数值分析领域中，贝塞尔曲线是计算机图形学中相当重要的参数曲线，其主要意义在于无论是直线还是曲线都能在数学上予以描述。最早贝塞尔曲线被用来对汽车主体进行设计，现在贝塞尔曲线被广泛地运用到计算机图形软件(如Photoshop、Flash等)中，是计算机图形领域重要的一个数学曲线。

<!--more-->

#贝塞尔曲线主要内容
&emsp;&emsp;贝塞尔曲线就是这样的一条曲线，它是依据四个位置任意的点坐标绘制出的一条光滑曲线。在历史上，研究贝塞尔曲线的人最初是按照已知曲线参数方程来确定四个点的思路设计出这种矢量曲线绘制法。贝塞尔曲线的有趣之处更在于它的皮筋效应，也就是说，随着点有规律地移动，曲线将产生皮筋伸引一样的变换，带来视觉上的冲击。1962年，法国数学家Pierre Bezier第一个研究了这种矢量绘制曲线的方法，并给出了详细的计算公式，因此按照这样的公式绘制出来的曲线就用他的姓氏来命名是为贝塞尔曲线。贝塞尔曲线按照阶数可以从一次扩展到n次，这里例举出常见的一次贝塞尔曲线、二次贝塞尔曲线和三次贝塞尔曲线。

##一次贝塞尔曲线
&emsp;&emsp;一次贝塞尔曲线，即线性贝塞尔曲线，其定义是:给定点P0、P1，贝塞尔曲线是两点间的一条直线。线性贝塞尔曲线由下列公式给出：
>B(t)=P0+(P1-P0)*t=(1-t)P0+t*P1,其中t是一个0到1之间的数值

&emsp;&emsp;该公式等同于对P1,P0两点进行线性插值。

![一次贝塞尔曲线](http://7wy477.com1.z0.glb.clouddn.com/imgs_一次贝塞尔曲线效果演示.gif)

##二次贝塞尔曲线
&emsp;&emsp;二次贝塞尔曲线的路径由给定点P0、P1、P2的函数B(t)给出:
>B(t)=(1-t)^2 * P0+2t(1-t)P1+t^2P2,其中t是一个0到1之间的数值

&emsp;&emsp;为构建二次贝塞尔曲线，可以中介点Q0和Q1作为由0至1的t:
* 由P0至P1的连续点Q0，描述一条线性贝塞尔曲线。
* 由P1至P2的连续点Q1，描述一条线性贝塞尔曲线。
* 由Q0至Q1的连续点B(t)，描述一条二次贝塞尔曲线。

![二次贝塞尔曲线原理图](http://7wy477.com1.z0.glb.clouddn.com/imgs_二次贝塞尔曲线原理.png)

![二次贝塞尔曲线演示效果](http://7wy477.com1.z0.glb.clouddn.com/imgs_二次贝塞尔曲线效果演示.gif)

##三次贝塞尔曲线
&emsp;&emsp;P0、P1、P2、P3四个点在平面或在三维空间中定义了三次方贝塞尔曲线。曲线起始于P0走向P1，并从P2的方向来到P3。P0和P1之间的间距，决定了曲线在转而趋进P3之前，走向P2方向的“长度有多长”。三次贝塞尔曲线的公式是:
>B(t)=P0(1-t)^3+3P1t(1-t)^2+3P2t^2(1-t)+P3t^3,其中t是一个0到1之间的数值

![三次贝塞尔曲线原理图](http://7wy477.com1.z0.glb.clouddn.com/imgs_三次贝塞尔曲线原理.png)

&emsp;&emsp;三次贝塞尔曲线的绘制这里采取的是一种已知曲线参数方程来确定四个点的方法，这种方法称为矢量曲线绘制法。这里以二维平面为例(如需三维空间同理构造出z(t)即可):
>x(t)=ax*t^3+bx*t^2+cx*t+x0
>y(t)=ay*t^3+by*t^2+cy*t+y0

&emsp;&emsp;因为x0、y0已知，因此我们可以用下列公式计算出剩余三个点的坐标:
>x1 = x0 + cx / 3
>x2 = x1 + ( cx + bx ) / 3
>x3 = x0 + cx + bx + ax

>y1 = y0 + cy / 3
>y2 = y1 + ( cy + by ) / 3
>y3 = y0 + cy + by + ay

&emsp;&emsp;在x0、y0已知的前提下，可以通过变换得到:
>cx = 3 * ( x1 - x0 )
>bx = 3 * ( x2 - x1 ) - cx
>ax = x3 - x0 - cx - bx

>cy = 3 * ( y1 - y0 )
>by = 3 * ( y2 - y1 ) - cy
>ay = y3 - y0 - cy - by

&emsp;&emsp;因此只要给定四个点，总能构造出一条三次贝塞尔曲线，这种方法是一种较为实用和可靠的方法。

![三次贝塞尔曲线效果演示](http://7wy477.com1.z0.glb.clouddn.com/imgs_三次贝塞尔曲线.gif)

&emsp;&emsp;下面我们来看一个简单的示例，该示例以Unity3D为基础建立，希望对大家理解贝塞尔曲线有所帮助。

# 代码示例
(尝试着实现了下，发现暂时有些问题，等实现了再更新上来吧！)

2015年12月19日更新：
在《CG Programming in Unity》一书中提到了贝塞尔曲线，实现了一个基础版本的贝塞尔曲线绘制，即在给定P0和P2的前提下，由用户通过滑杆对P1进行控制，可以实时预览当前曲线的样式，感兴趣的朋友可以阅读该书的第9章部分。下面给出代码示例：

```
using UnityEngine;
using System.Collections;

public class QuadraticBezier : MonoBehaviour 
{

    /// <summary>
    /// 3个基础点
    /// </summary>
    public Vector3 P0;
    public Vector3 P1;
    public Vector3 P2;

    /// <summary>
    /// 调整第二个参数的水平分量
    /// </summary>
    private float paramX = 0.5f;

    /// <summary>
    /// 调整第二个参数的垂直分量
    /// </summary>
    private float paramY = 0.5f;

    /// <summary>
    /// 线条宽度
    /// </summary>
    public float LineWidth = 0.15f;

    /// <summary>
    /// 线条颜色
    /// </summary>
    public Color LineColor = Color.white;

    /// <summary>
    /// 顶点数目
    /// </summary>
    public int PointsCount = 10;

    /// <summary>
    /// 线渲染器
    /// </summary>
    private LineRenderer lineRenderer;


    void Start () 
    {
        //初始化线渲染器
        lineRenderer = this.GetComponent<LineRenderer>();
        if(lineRenderer == null)
            lineRenderer = this.gameObject.AddComponent<LineRenderer>();
        lineRenderer.useWorldSpace = true;
        lineRenderer.SetColors(LineColor,LineColor);
        lineRenderer.material = new Material(Shader.Find("Particles/Additive"));
        lineRenderer.SetWidth(LineWidth,LineWidth);
        lineRenderer.SetVertexCount(PointsCount);
    }


    void Update() 
    {
        //根据滑杆参数计算P1
        P1 = new Vector3(Mathf.Abs(P0.x-P2.x) * paramX, Mathf.Abs(P0.x-P2.x) * paramY, 0);

        //绘制曲线
        for (int i = 0; i < PointsCount; i++)
        {
            float t = i / (PointsCount - 1.0f);
            Vector3 position = (1.0f - t) * (1.0f - t) * P0 + 2.0f * (1.0f - t) * t * P1 + t * t * P2;
            lineRenderer.SetPosition(i, position);
        }
    }

    void OnGUI()
    {
        GUILayout.Label(string.Format("第一个参数：P0=({0},{1},{2})", P0.x, P0.y, P0.z));
        GUILayout.Label(string.Format("第二个参数：P1=({0},{1},{2})", P1.x, P1.y, P1.z));
        GUILayout.Label("请拖动下面的滑杆调整第二个参数P1观察曲线变化");
        paramX = GUILayout.HorizontalSlider(paramX, 0, 1);
        paramY = GUILayout.HorizontalSlider(paramY, 0, 1);
        GUILayout.Label(string.Format("第三个参数：P2=({0},{1},{2})", P2.x, P2.y, P2.z));
    }
}
```

在这段代码中通过两个参数来调整P1，这里取Z分量为0主要是方便研究，扩展到三维空间需要给定第三个参数来对应的调整。这个示例的运行效果如下：

![效果展示]()