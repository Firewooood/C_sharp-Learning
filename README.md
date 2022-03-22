# C_sharp-Learning
关于C#的一些笔记
# 委托
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

********************
