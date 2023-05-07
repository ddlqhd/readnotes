---
layout: post
title:  "[Effective C#] 第7条：用委托表示回调"
date:   2023-05-07 11:44:00 +0800
categories: c#
tag: Effective C#
---

C#语言用委托来表示回调。通过委托，可以定义类型安全的回调。

使用场景：
- 事件处理
- 类之间沟通：如果想采用比接口更为松散的方式在类之间沟通，那么就应该考虑委托。

委托是一种对象，其中含有指向方法的引用，这个方法既可以是静态方法，又可以是实例方法。

常见的委托形式：
- predicate（谓词）是用来判断某条件是否成立的布尔（Boolean）函数
- 而Func<>则会根据一系列的参数求出某个结果
- Action<>接受任意数量的参数，其返回值的类型是void

只要.NET框架需要调用方提供某个方法，它就会使用委托，从而令调用方能以lambda表达式的形式来提供该方法。你自己在设计API时，也应该遵循同样的惯例，使得调用这个API的人能够以lambda表达式的形式指定回调。

由于历史原因，所有的委托都是多播委托（multicast delegate），也就是会把添加到委托中的所有目标函数（target function）都视为一个整体去执行。这就导致有两个问题需要注意：
- 第一，程序在执行这些目标函数的过程中可能发生异常；
- 第二，程序会把最后执行的那个目标函数所返回的结果当成整个委托的结果。
多播委托在执行的时候，会依次调用这些目标函数，而且不捕获异常。因此，只要其中一个目标抛出异常，调用链就会中断，从而导致其余的那些目标函数都得不到调用。

如下：cp绑定了两个目标函数，在执行时会依次调用，而返回值由最后一个函数也就是CheckWithSystem()决定
{% highlight csharp %}
public void LengthyOperation(Func<bool> pred)
{
  foreach (ComplicatedClass cl in container)
  {
      cl.DoLengthyOperation();
      // Check for user abort:
      if (false == pred())
        return;
  }
}
{% endhighlight %}

{% highlight csharp %}
Func<bool> cp = () => CheckWithUser();
cp += () => CheckWithSystem();
c.LengthyOperation(cp);
{% endhighlight %}
异常与返回值这两个问题可以通过手动执行委托来解决。由于每个委托都会以列表的形式来保存其中的目标函数，因此只要在该列表上面迭代，并把这些目标函数轮流执行一遍就可以了：
{% highlight csharp %}
public void LengthyOperation2(Func<bool> pred)
{
  bool bContinue = true;
  foreach (ComplicatedClass cl in container)
  {
      cl.DoLengthyOperation();
      foreach (Func<bool> pr in pred.GetInvocationList())
        bContinue &= pr();
      
      if (!bContinue)
        return;
  }
}
{% endhighlight %}