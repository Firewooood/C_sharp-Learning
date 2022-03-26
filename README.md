# 委托
## 委托就是可以用方法名调用另一方法的便捷方法
 委托是用户自定义的类，它定义了方法的类型。储存的是一系列具有相同参数和返回类型方法的地址列表，调用委托时，此委托列表的所有方法都将被执行。
## 为什么要使用委托
```C#
//遍历列表，返回大于等于10的所有数
List <int> Traverse(List<int> nums){
    var list = new List<int>();
    foreach(var num in nums){
    //将if中的内容变为一个方法，则可对整个代码实现可复用
        if(num >= 10) //num % 2 == 0
         list.Add(num);
    }
    return list
}
```
## 怎么使用委托
```C# 
//声明委托
delegate bool Function(int num);
//即可在if中直接使用委托
List <int> Traverse(List<int> nums, Function function){
    var list = new List<int>();
    foreach(var num in nums){
    //将if中的内容变为一个方法，则可对整个代码实现可复用
        if(function(num))
         list.Add(num);
    }
    return list
}
```

```C#
//传入委托
Function GreaterThan10 = delegate(int n)
{return n >= 10;};
Function IsEven = delegate(int n)
{return n % 2 == 0;};
//使用上面给出的Traverse函数
Traverse(new List<int>(){1,2,3,4,5,6,7},GreaterThan10);
```
使用委托则函数Traverse被**封闭**住了，复用时，无需该表Traverse内部的代码     

# Lambda 对**委托**的化简写法
## 语句Lambda 和 表达式Lambda
* (type var, ...) => {...}  
* (var, ...) => {...}  
* var => {...}  
* () => {...}

在上方的委托中可将其替换为
```C#
Traverse(new List<int>(){1,2,3,4,5,6,7},(int n) => {return n % 2 == 0});
//上面的表达式也可 简化成(var, ...) => {...} 
Traverse(new List<int>(){1,2,3,4,5,6,7},(n) => {return n % 2 == 0});
//还可简化 为表达式lambda ，只可有一条语句，指向的默认为return
Traverse(new List<int>(){1,2,3,4,5,6,7},n => n%2 == 0);
```

# 多播委托
 委托对象的一个有用属性在于可通过使用 运算符将多个对象分配到一个委托实例。 多播委托包含已分配委托列表。 此多播委托被调用时会依次调用列表中的委托。 仅可合并**类型相同**的委托。
```C#
Action<int> all;
Action<int> AddThenPrint = i => {i++;Console.WriteLine(i);};
Action<int> Print = i => C;onsole.WriteLine(i);

//使用
all = AddThenPrint + Print;
all -= print;
all(1); //输出结果为2；
```

# 事件
事件（Event） 基本上说是一个用户操作，如按键、点击、鼠标移动等等，或者是一些提示信息，如系统生成的通知。应用程序需要在事件发生时响应事件。例如，中断。  

C# 中使用事件机制实现线程间的通信。
类 或对象可以通过事件向其他类或对象通知发生的相关事情。 发送（或引发）事件的类称为“发布者”，接收（或处理）事件的类称为“订阅者”。

## 声明事件
在类的内部声明事件，首先必须声明该事件的委托类型。例如：
```C#
public delegate void BoilerLogHandler(string status);
```
然后，声明事件本身，使用 event 关键字：
```C#
// 基于上面的委托定义事件
public event BoilerLogHandler BoilerEventLog;
```

# 消息机制
消息机制是windows的基石。  
每个消息号，都有自己的参数格式，编程时，需要查阅文档根据格式要求来写代码任何支持事件驱动的编程语言都要遵守这些消息号的标准.

windows程序会产生很多消息，比如你单击鼠标，移动窗口都会产生消息。这个函数就是默认的消息处理函数。你可以重载这个函数来制定自己的消息处理流程.

在Winform程序中，可以**重写WndProc函数**，来捕捉所有发生的窗口消息。

这样，我们就可以"篡改"传入的消息，而人为的让窗口改变行为。
*  ### 案例一：捕获窗体消息 
   显示USB的插拔状态**WM_DEVICECHANGE**
```C#
protected override void WndProc(ref Message m)
        {
            if (m.Msg == 0x219)
            {
                label1.Text = "yes";
            }
            base.WndProc(ref m);
        }
```
* ### 案例二 控件捕获消息
    模拟鼠标点击事件    
    #define WM_LBUTTONDOWN 0x0201
```C#
    const int WM_LBUTTONDOWN = 0x201;
    protected override void WndProc(ref Message m)
    {
        if (m.Msg == WM_LBUTTONDOWN)
        {
            Console.WriteLine("WndProc中左键按下");
        }
        base.WndProc(ref m);
}
```
实现自定义组件Mybutton点击事件    
用户单击了窗体中的按钮后，Windows就会给按钮消息处理程序(WndPro)发送一个WM_MOUSECLICK息，对于.NET开发人员来说，就是按钮的OnClick事件。
事件接收器是指在发生某些事情时**被通知的任何应用程序、对象或组件（在Demo中可以理解为Button类的实例btn）**。有事件接收器就有事件发送器。发送器的作用就是引发事件。发送器可以是应用程序中的另一个对象或者程序集（在上面的Demo中可以理解为程序集中的program引发事件），实际在系统事件中，例如鼠标单击或键盘按键，发送器就是.NET运行库。注意，事件的发送器并不知道接收器是谁。这就使得事件非常有用。
现在，在事件接收器的某个地方有一个**方法（在Demo中位OnClick）**，它负责**处理事件**。在每次发生已注册的事件时执行事件处理程序（btn_Click）。此时就要用到委托了。由于发送器对接收器一无所知，所以无法设置两者之间的引用类型，而是使用委托作为中介，发送器定义接收器要使用的委托，接受器将事件处理程序注册到事件中，接受事件处理程序的过程成为封装事件。
```C#
public new EventHandler<MyButtonArgs> Click; //实例化委托EventHandler，对象Click
protected override void WndProc(ref Message m)
        {
            if (m.Msg == WM_LBUTTONDOWN)
            {
                //此处的Click操作相当于占位，后续注册的方法会被填入此
                Click(this,new MyButtonArgs(50,100));
            //第一个参数表示引发事件的对象，第二个参数表示引发事件后所要传给处理程序的参数。
            }
            base.WndProc(ref m);
        }
```
在form1.Designer.cs中
```C#
this.myButton1.Click += new EventHandler<MyButtonArgs>(this.MyButton1_Click);
//将MyButton1_Click方法通过委托添加到 Click 事件中
```
上面的实现原理是：由WindProc截取鼠标左键单击的消息，将消息赋到委托之上，由外部直接去调用

********************
# WinForm
From.show/ShowDialog(this) 从一Form1向另一Form2中传值，需将本地控件this传递过去。  可在form2中使用 **(this.Owner as Form1).label1.Text = "123";** 来修改Form1中的内容。

GroupBox 是容器，容器可以包含其他组件。

PictureBox 的**SizeMode**属性
* Normal 模式中，Image 置于 PictureBox 的左上角，凡是因过大而不适合 PictureBox 的任何图像部分都将被剪裁掉。
* StretchImage 值会使图像拉伸或收缩，以便适合 PictureBox。
* 使用 AutoSize 值会使控件调整大小，以便总是适合图像的大小。
* 使用 Zoom 的值可以使图像被拉伸或收缩以适应 PictureBox；但是仍然保持原始纵横比。
