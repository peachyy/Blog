title: 教你一步步实现一个虚拟摇杆
date: 2015-10-30 09:44:18
categories: [Unity3D]
tags: [游戏开发,虚拟摇杆,uGUI]
---
&emsp;&emsp;各位朋友，大家好，我是秦元培，欢迎大家关注我的博客，我的博客地址是[http://qinyuanpei.com](http://qinyuanpei.com)。最近因为项目需要决定尝试自己来实现一个虚拟摇杆，所以在今天的文章中我们的目标是使用uGUI来制作一个可以在移动平台稳定运行的虚拟摇杆(请不要问我为什么不使用NGUI来实现，你说我做个虚拟摇杆有必要在项目里导入那么多的资源嘛23333)。关于使用第三方插件来实现虚拟摇杆，请大家参照我以前写的文章[Unity3D游戏开发之使用EasyTouch虚拟摇杆控制人物移动](http://blog.csdn.net/qinyuanpei/article/details/26204177)，在这里就不再赘述了。

<!--more-->

&emsp;&emsp;虚拟摇杆这种输入方式相信大家在手机游戏平台上已经相当的熟悉了，首先我们来简单了解下虚拟摇杆的设计原理。虚拟摇杆有一张固定的2D贴图(背景层)和一张可拖动的2D贴图(控制层)构成，默认情况下控制层在背景层的中心，我们称这个位置为初始位置，当移动控制层后移动层的位置会发生变化，此时控制层的当前位置和初始位置两点间可以计算出一个2D向量，通过这个向量我们就可以判断虚拟摇杆的移动方向。在经典的八方向摇杆导航中摇杆中可移动方向被分成了上、左上、右上、下、左下、右下、左、右共8个方向。我们知道根据三角函数可以非常容易地计算出这个2D向量的角度并由此判定摇杆是在向着这8个方向中的哪一个方向移动。在今天的文章中，我们不需要考虑这8个方向，因为我们可以向任何一个方向进行移动。

&emsp;&emsp;好了，首先在场景中创建两个Image组件和一个空的游戏体，然后将这两个Image组件拖拽到这个空的游戏体下使它们称为其子节点。这里需要注意的是这两个Image的层级关系。现在我们来编写脚本，这个脚本将被添加到控制层物体上：
```

/*
 * uGUI虚拟摇杆
 * 作者：秦元培
 * 博客：http://qinyuanpei.com
 * 时间：2015年10月24日
 */

using UnityEngine;
using System.Collections;
using UnityEngine.UI;
using UnityEngine.EventSystems;

public class JoyStick : MonoBehaviour,IPointerDownHandler, IPointerUpHandler, IDragHandler
{

    /// <summary>
    /// 摇杆最大半径
    /// 以像素为单位
    /// </summary>
    public float JoyStickRadius = 50;

    /// <summary>
    /// 摇杆重置所诉
    /// </summary>
    public float JoyStickResetSpeed = 5.0f;

    /// <summary>
    /// 当前物体的Transform组件
    /// </summary>
    private RectTransform selfTransform;

    /// <summary>
    /// 是否触摸了虚拟摇杆
    /// </summary>
    private bool isTouched = false;

    /// <summary>
    /// 虚拟摇杆的默认位置
    /// </summary>
    private Vector2 originPosition;

    /// <summary>
    /// 虚拟摇杆的移动方向
    /// </summary>
    private Vector2 touchedAxis;
    public Vector2 TouchedAxis
    {
        get
        {
            if(touchedAxis.magnitude < JoyStickRadius)
                return touchedAxis.normalized / JoyStickRadius;
            return touchedAxis.normalized;
        }
    }

	/// <summary>
	/// 定义触摸开始事件委托 
	/// </summary>
	public delegate void JoyStickTouchBegin(Vector2 vec);

    /// <summary>
    /// 定义触摸过程事件委托 
    /// </summary>
    /// <param name="vec">虚拟摇杆的移动方向</param>
	public delegate void JoyStickTouchMove(Vector2 vec);

    /// <summary>
    /// 定义触摸结束事件委托
    /// </summary>
	public delegate void JoyStickTouchEnd();

    /// <summary>
    /// 注册触摸开始事件
    /// </summary>
	public event JoyStickTouchBegin OnJoyStickTouchBegin;

    /// <summary>
    /// 注册触摸过程事件
    /// </summary>
	public event JoyStickTouchMove OnJoyStickTouchMove;

    /// <summary>
    /// 注册触摸结束事件
    /// </summary>
	public event JoyStickTouchEnd OnJoyStickTouchEnd;

	void Start () 
    {
        //初始化虚拟摇杆的默认方向
        selfTransform = this.GetComponent<RectTransform>();
        originPosition = selfTransform.anchoredPosition;
	}


    public void OnPointerDown(PointerEventData eventData)
    {
        isTouched = true;
		touchedAxis = GetJoyStickAxis(eventData);
		if(this.OnJoyStickTouchBegin != null)
			this.OnJoyStickTouchBegin(TouchedAxis);
    }

    public void OnPointerUp(PointerEventData eventData)
    {
        isTouched = false;
		selfTransform.anchoredPosition = originPosition;
		touchedAxis = Vector2.zero;
		if(this.OnJoyStickTouchEnd != null)
			this.OnJoyStickTouchEnd();
    }

    public void OnDrag(PointerEventData eventData)
    {
		touchedAxis = GetJoyStickAxis(eventData);
		if(this.OnJoyStickTouchMove != null)
			this.OnJoyStickTouchMove(TouchedAxis);
    }


    void Update()
    {
        //当虚拟摇杆移动到最大半径时摇杆无法拖动
        //为了确保被控制物体可以继续移动
        //在这里手动触发OnJoyStickTouchMove事件
        if(isTouched && touchedAxis.magnitude>=JoyStickRadius)
        {
            if(this.OnJoyStickTouchMove != null)
                this.OnJoyStickTouchMove(TouchedAxis);
        }

        //松开虚拟摇杆后让虚拟摇杆回到默认位置
        if(selfTransform.anchoredPosition.magnitude > originPosition.magnitude)
            selfTransform.anchoredPosition -= TouchedAxis * Time.deltaTime * JoyStickResetSpeed;
    }

	/// <summary>
	/// 返回虚拟摇杆的偏移量
	/// </summary>
	/// <returns>The joy stick axis.</returns>
	/// <param name="eventData">Event data.</param>
	private Vector2 GetJoyStickAxis(PointerEventData eventData)
	{
		//获取手指位置的世界坐标
		Vector3 worldPosition;
		if (RectTransformUtility.ScreenPointToWorldPointInRectangle (selfTransform,
		         eventData.position, eventData.pressEventCamera, out worldPosition))
			selfTransform.position = worldPosition;
		//获取摇杆的偏移量
		Vector2 touchAxis = selfTransform.anchoredPosition-originPosition;
		//摇杆偏移量限制
		if(touchAxis.magnitude >= JoyStickRadius) 
		{
			touchAxis = touchAxis.normalized * JoyStickRadius;
			selfTransform.anchoredPosition = touchAxis;
		}
		return touchAxis;
	}


}


```
&emsp;&emsp;在这段脚本中，我们实现了OnPointerDown、OnPointerUp和OnDrag三个uGUI事件接口，然后注册了相关的事件委托，这里借鉴了EasyTouch的设计，可以使得虚拟摇杆的逻辑和角色控制逻辑相互分离。这里的核心方法是GetJoyStickAxis()方法，通过这个方法我们可以获得一个Vector2类型的值，它表示的是未标准化过的虚拟摇杆的偏移量。这里的RectTransformUtility.ScreenPointToWorldPointInRectangle()方法表示将一个屏幕坐标转化为对应RectTransform的世界坐标，RectTransform的anchoredPosition属性表示的是当前元素在场景中的屏幕坐标。我们知道屏幕坐标是以像素为单位的，因此这里使用屏幕坐标可以计算出虚拟摇杆在水平方向和垂直方向上移动了多少个像素，我们以此来作为虚拟摇杆的偏移量衡量指标。TouchedAxis是经过标准化以后的偏移量，我们将把这个值传递到事件委托中以提供给外部来调用。好了，要说的就这些了，没有说到的大家可以看看代码里的注释或者是在博客中给我留言，就是这样啦。

&emsp;&emsp;接下来，我们在场景中添加一个角色模型来测试我们编写的虚拟摇杆，因为在JoyStick中我们已经定义了事件委托，所以在这里就是简单的调用啦。好了，我们一起来看看代码吧！

```

/*
 * Joystick3D.cs
 * 3D模式下的虚拟摇杆测试
 * 作者：秦元培
 * 博客：http://qinyuanpei.com
 * 时间：2015年10月30日
 */

using UnityEngine;
using System.Collections;

public class JoyStick3D : MonoBehaviour 
{

	private JoyStick js;
	
	void Start () 
	{
		js = GameObject.FindObjectOfType<JoyStick> ();
		js.OnJoyStickTouchBegin += OnJoyStickBegin;
		js.OnJoyStickTouchMove += OnJoyStickMove;
		js.OnJoyStickTouchEnd += OnJoyStickEnd;
	}
	

	void OnJoyStickBegin(Vector2 vec)
	{
        Debug.Log("开始触摸虚拟摇杆");
	}

	void OnJoyStickMove (Vector2 vec)
	{
        Debug.Log("正在移动虚拟摇杆");

        //设置角色朝向
		Quaternion q = Quaternion.LookRotation (new Vector3 (vec.x, 0, vec.y));
		transform.rotation = q;

        //移动角色并播放奔跑动画
		transform.Translate(Vector3.forward * 75f * Time.deltaTime);
		animation.CrossFade("Run");
	}
	
	void OnJoyStickEnd ()
	{
        Debug.Log("触摸移动摇杆结束");

        //播放默认待机动画
		animation.CrossFade("idle");
	}

    void OnGUI()
    {
        GUI.Label(new Rect(30,30,200,30),"3D模式下的虚拟摇杆测试");
    }
}

```

&emsp;&emsp;最终程序的运行效果如下图所示，我们编写的这个虚拟摇杆可以在手机上完美的运行，欢饮大家来一起测试和吐槽！

![2D模式演示](http://img.blog.csdn.net/20151030145705436)

![3D模式演示](http://img.blog.csdn.net/20151030150441747)

&emsp;&emsp;好了，今天的内容就是这样啦！欢迎大家继续关注我的博客，希望大家喜欢！





