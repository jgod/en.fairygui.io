---
title: Used in Lua
type: guide_unity
order: 80
---

## ToLua

**安装**

1. 将[LuaUIHelper.cs](https://github.com/fairygui/FairyGUI-unity/blob/master/LuaSupport/ToLua/LuaUIHelper.cs)放入你的工程。[TweenUtils.cs](https://github.com/fairygui/FairyGUI-unity/blob/master/LuaSupport/ToLua/TweenUtils.cs)是DOTween的一个辅助工具类，如果你需要就放入，不需要就不放，不是必须的。

2. 将以下语句添加到`CustomSettings.cs`适当的位置，然后重新生成绑定文件。在实际使用中，如果你还用到这里没有的其他类，自己添加即可。
```csharp
    _GT(typeof(EventContext)),
    _GT(typeof(EventDispatcher)),
    _GT(typeof(EventListener)),
    _GT(typeof(InputEvent)),
    _GT(typeof(DisplayObject)),
    _GT(typeof(Container)),
    _GT(typeof(Stage)),
    _GT(typeof(FairyGUI.Controller)),
    _GT(typeof(GObject)),
    _GT(typeof(GGraph)),
    _GT(typeof(GGroup)),
    _GT(typeof(GImage)),
    _GT(typeof(GLoader)),
    _GT(typeof(PlayState)),
    _GT(typeof(GMovieClip)),
    _GT(typeof(TextFormat)),
    _GT(typeof(GTextField)),
    _GT(typeof(GRichTextField)),
    _GT(typeof(GTextInput)),
    _GT(typeof(GComponent)),
    _GT(typeof(GList)),
    _GT(typeof(GRoot)),
    _GT(typeof(GLabel)),
    _GT(typeof(GButton)),
    _GT(typeof(GComboBox)),
    _GT(typeof(GProgressBar)),
    _GT(typeof(GSlider)),
    _GT(typeof(PopupMenu)),
    _GT(typeof(ScrollPane)),
    _GT(typeof(Transition)),
    _GT(typeof(UIPackage)),
    _GT(typeof(Window)),
    _GT(typeof(GObjectPool)),
    _GT(typeof(Relations)),
    _GT(typeof(RelationType)),
    _GT(typeof(Timers)),
    
    _GT(typeof(LuaUIHelper)),
    _GT(typeof(GLuaComponent)),
    _GT(typeof(GLuaLabel)),
    _GT(typeof(GLuaButton)),
    _GT(typeof(GLuaProgressBar)),
    _GT(typeof(GLuaSlider)),
    _GT(typeof(GLuaComboBox)),
    _GT(typeof(LuaWindow)),
    
    _GT(typeof(TweenUtils))
```

3. 将[FairyGUI.lua](https://github.com/fairygui/FairyGUI-unity/blob/master/LuaSupport/ToLua/FairyGUI.lua)放入你的lua文件存放目录。

**监听事件**

1. 普通方法的侦听和删除侦听
```csharp
    require 'FairyGUI'
    
    function OnClick()
        print('you click')
    end
    
    --也可以带上事件参数
    function OnClick(context)
        print('you click'..context.sender)
    end
    
    UIPackage.AddPackage('Demo')
    local view = UIPackage.CreateObject('Demo', 'DemoMain')
    GRoot.inst:AddChild(view)
    
    view.onClick:Add(OnClick)
    --view.onClick:Remove(OnClick)
    --view.onClick:Set(OnClick)
```

2. 类方法的侦听和删除侦听
```csharp
    function TestClass:OnClick()
        print('you click')
    end
    
    --也可以带上事件参数
    function TestClass:OnClick(context)
        print('you click'..context.sender.name)
    end
    
    self.view.onClick:Add(TestClass.OnClick, self)
    --self.view.onClick:Remove(TestClass.OnClick, self)
```

**使用Window类**

FairyGUI提供的Window类，一般需要开发者自己扩展，例如覆盖OnShown,OnHide等。在Lua里，编写Window扩展的方法是：
```csharp
    WindowBase = fgui.window_class()
    
    --构建函数
    function WindowBase:ctor()
    end
    
    --可覆盖的函数(可选，不是说必须）
    function WindowBase:OnInit()
    end
    
    function WindowBase:OnShown()
    end
    
    function WindowBase:OnHide()
    end
    
    function WindowBase:DoShowAnimation()
    end
    
    function WindowBase:DoHideAnimation()
    end
```

然后还可以继续以上得到的Window类，例如:
```csharp
    MyWindow = fgui.window_class(WindowBase)

    function MyWindow:OnInit()
        WindowBase.OnInit(self)
    end
```

**自定义扩展**

FairyGUI在C#里可以使用UIObjectFactory.SetPackageItemExtension进行自定义扩展。在Lua里，同样可以这样做。方法如下：

1. 定义扩展类。注意基础类型，不要搞错。例如按钮是GButton，一般组件是GComponent。
```csharp
    MyButton = fgui.extension_class(GButton)
    
    --注意这里不是构造函数，是当组件已经（通过XML）构建完毕后调用的
    function MyButton:ctor()
        print(self:GetChild('n1'))
    end
    
    --添加自定义的方法和字段
    function MyButton:Test()
        print('test')
    end
    
    local get = tolua.initget(MyButton)
    local set = tolua.initset(MyButton)
    get.myProp = function(self)
        return self._myProp
    end
    
    set.myProp = function(self, value)
        self._myProp = value
        self:GetChild('n1').text = value
    end
```

2. 注册扩展类。要在创建任何对象前注册好。
```csharp
    fgui.register_extension("ui://包名/我的按钮", MyButton)
```

3. 完成以上两步后，任何“我的按钮”这个资源创建出来的对象都可以使用MyButton访问了。例如：
```csharp
    local myButton = someComponent:GetChild("myButton") --这个myButton的资源是“我的按钮”
    myButton:Test()
    myButton.myProp = 'hello'
    
    local myButton2 = UIPackage.CreateObject("包名","我的按钮")
    myButton2:Test()
    myButton2.myProp = 'world'
```

## SLua

上面ToLua的经验也很多适用的，请先参阅。
网络参考资料：[Unity SLua+FairyGUI](http://blog.csdn.net/LMLBY/article/details/61915381)

欢迎补充。

## XLua

上面ToLua的经验也很多适用的，请先参阅。
网络参考资料：[FairyGUI导出XLua接口](http://blog.csdn.net/mingtingjian/article/details/62046909)

欢迎补充。