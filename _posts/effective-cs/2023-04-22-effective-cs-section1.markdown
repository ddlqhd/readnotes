---
layout: post
title:  "[Effective C#] 第1条：优先使用隐式类型的局部变量"
date:   2023-04-22 22:09:17 +0800
categories: "effective-cs"
tag: Effective C#
---

如果你是从其他语言转入C#的，那么需要学习C#语言自己的编程习惯，使得这门语言能够促进你的工作，而不是阻碍你的工作。本章会提醒大家把那些与C#编程风格不符的做法改掉，并培养正确的编程习惯。

# 第1条：优先使用隐式类型的局部变量
隐式类型的局部变量(var)是为了支持匿名类型机制而加入C#语言的。用var来声明变量而不指明其类型，可以**令开发者把注意力更多地集中在名称上面**，从而更好地了解其含义。

**用var来声明的变量不是动态变量**，它的类型会根据赋值符号右侧那个值的类型来确定。var的意义在于，你不用把变量的类型告诉编译器，编译器会替你判断。

在很多情况下，完全可以使用var来声明隐式类型的局部变量，因为编译器会自动选择合适的类型。但是**不能滥用这种方式，因为那样会令代码难于阅读，甚至可能产生微妙的类型转换bug**。

例如：
{% highlight csharp %}
var foo = new MyType(); // good
var result = someObj.DoSomeWork(parameter); // bad
{% endhighlight %}
在someObj.DoSomeWork的例子中，维护人员在阅读代码时无法推断出确定的类型，可能导致维护人员推断的类型与编译器推断的类型不一致，从而在修改代码时引入不必要的bug。

如果隐式类型的局部变量的类型是C#内置的数值类型，那么还会产生另外一些问题，因为在使用这样的数值时，可能会触发各种形式的转换（主要是narrowing conversion的问题，比如Doule转float，此种转换会有精度丢失）。
{% highlight csharp %}
var f = GetMagicNumber();
var total = 100 * f / 6;
{% endhighlight %}
此情况下total的类型由f的类型决定，同时表达式的精度也有f决定，而f又是编译器根据GetMagicNumber()函数的返回值类型推断出来的，此时如果明确指定f的变量类型，可能两种结果：一是GetMagicNumber()可以隐式转换到f的类型，此时程序正常运行；二是，GetMagicNumber()无法隐式转换到f的类型，此时编译器会报错，但是不管哪种情况，维护人员可以通过明确的类型来更容易的发现代码的问题。因此，**用隐式类型的局部变量来表示数值的时候要多加小心**，因为可能会发生很多隐式转换，这不仅容易令阅读代码的人产生误解，而且其中某些转换还会令精确度下降。

但是，相反有些情况下，编译器所选择的类型可能比开发者选择的类型更合理。
{% highlight csharp %}
public IEnumerab1e<string> FindCustomersStartingWith1(string Start)
{
    IEnumerab1e<string> q = 
        from c in db.Customers
        select c.ContactName;
    var q2 = q.Where(s => s.StartsWith(start));
    return q2;
}
{% endhighlight %}
这段代码有严重的性能问题。第一行查询语句会把每一个人的姓名都从数据库里取出来，由于它要查询数据库，因此，其返回值实际上是IQueryable<string>类型，但是开发者却把保存该返回值的变量q声明成了IEnumerable<string>类型。由于IQueryable<T>继承自IEnumerable<T>，因此编译器并不会报错，但是这样做将导致后续的代码无法使用由IQueryable所提供的某些特性。接下来的那行查询语句，就受到了这样的影响，它本来可以使用Queryable.Where去查询，但是却用了Enumerable.Where。如果开发者不把变量q的类型明确指定为IEnumerable<string>，那么编译器就可以将其设为更加合适的IQueryable<string>类型了。假如IQueryable<string>不能隐式地转换成IEnumerable<string>，那么刚才那种写法会令编译器报错。但实际上是可以完成隐式转换的，因此编译器不会报错，这使得开发者容易忽视由此引发的性能问题。

如果使用var自动类型推导，q的类型编译器会指定为IQueryable<string>类型，IQueryable能够把与数据查询有关的多个表达式树组合成一项操作，以便一次执行完毕，而且通常是在存放数据的远程服务器上面执行的。
{% highlight csharp %}
public IEnumerab1e<string> FindCustomersStartingWith1(string Start)
{
    var q = 
        from c in db.Customers
        select c.ContactName;
    var q2 = q.Where(s => s.StartsWith(start));
    return q2;
}
{% endhighlight %}

写程序的时候，如果发现编译器自动选择的类型有可能令人误解代码的含义，使其无法立刻看出这个局部变量的准确类型，那么就应该把类型明确指出来，而不要采用var来声明。反之，如果读代码的人根据代码本身的语义所推测出的类型与编译器自动选择的类型相符，那就可以用var来声明。例如对int、float、double等数值型的变量，就应该明确指出其类型，而对其他变量则不妨使用var来声明。