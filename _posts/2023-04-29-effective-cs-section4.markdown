---
layout: post
title:  "[Effective C#] 第4条：用内插字符串取代string.Format()"
date:   2023-04-29 22:26:00 +0800
categories: c#
---

## string.Format的问题
- 所有的替换操作都是根据格式字符串里面的序号来完成的，而编译器又不会去验证格式字符串后面的参数个数与有待替换的序号数量是否相等。如果两者不等，那么程序在运行的时候就会抛出异常。
- 格式字符串中的序号与params数组中的位置相对应，而阅读代码的人却不太容易看出来数组中的那些字符串是不是按照正确顺序排列的。必须运行代码，并仔细检查程序所生成的字符串，才能够确认这一点。


## 内插字符串的用法
内插字符串以$开头，它不像传统的格式字符串那样把序号放在一对花括号里面，并用其指代params数组中的对应元素，而是可以直接在花括号里面编写C#表达式

### 基本使用
内插字符串会在必要的时候把变量从其他类型转为string类型，比如 

{% highlight csharp %}
Console.WriteLine($"This is PI {Math.PI}.");
{% endhighlight %}

由字符串内插操作所生成的代码会调用一个参数为params对象数组的格式化方法。Math.PI是double类型，而double是值类型，因此，必须将其自动转为Object才可以。这种转换需要执行装箱操作，如果刚才那行代码运行得很频繁，或是需要在短小的循环中反复执行，那么就会严重影响性能（关于这个问题，请参见本章第9条）。这种情况下，开发者应该自己去把它转换成字符串，这样就不用给表达式中的数值装箱了：

{% highlight csharp %}
Console.WriteLine($"This is PI {Math.PI.ToString()}.");
{% endhighlight %}

### 控制输出格式
可以通过冒号 ":" 后面跟格式控制符的方式，控制内插字符串的输出格式
{% highlight csharp %}
Console.WriteLine($"This is PI {Math.PI:F2}.");
{% endhighlight %}

由于 ":" 作为一个特殊字符，此时如果表达式里面有冒号的话，需要特殊处理下，比如用 "()" 将内容括起来，显式的告诉编译器如何处理
{% highlight csharp %}
Console.WriteLine($"This is PI {(round ? Math.PI : Math.PI.ToString())}.");
{% endhighlight %}

### 支持嵌套
在内插字符串中还可以使用内插字符串
{% highlight csharp %}
string result = default(string);
Conso1e.WriteLine($@"Record is {(records.TryGetValue(index, out result) ? result 
    : $"No record found at index {index}")}");
{% endhighlight %}


