---
layout: post
title:  "[Effective C#] 第3条：优先考虑is或as运算符，尽量少用强制类型转换"
date:   2023-04-23 21:47:00 +0800
categories: effective-cs
tag: Effective C#
---

有些场景下需要在运行期进行类型转换（比如：框架代码使用object作为参数），此时有两种办法能够实现转换，
- 一是使用as运算符
- 二是通过强制类型转换（cast）来绕过编译器的类型检查。

在这之前，可以先通过is判断该操作是否合理，然后再使用as运算符或执行强制类型转换。

推荐优先使用as运算符，其比cast更安全，且更有效率（不需要使用try catch结构，且大多数场景不需要为了类型转换创建新的对象（但若用as运算符把装箱的值类型转换成未装箱且可以为null的值类型，则会创建新的对象））。
**强制类型转换在遇到null的时候并不抛出异常**，这导致开发者必须处理两种特殊情况：一种是o本来就为null，因此强制转换后所得的t也是null；另一种是程序因无法转换到目标类型而抛出异常。

as运算符与强制类型转换之间的最大区别在于如何对待由用户所定义的转换逻辑。**as与is运算符只会判断待转换的那个对象在运行期是何种类型，并据此做出相应的处理，除了必要的装箱与取消装箱操作，它们不会执行其他操作**。如果待转换的对象既不属于目标类型，也不属于由目标类型所派生出来的类型，那么as操作就会失败。反之，强制类型转换操作则有可能使用某些**类型转换逻辑**来实现类型转换，这不仅包含由用户所定义的类型转换逻辑，而且还包括内置的数值类型之间的转换。例如可能发生从long至short的转换，这种转换可能导致信息丢失。
{% highlight csharp %}
public class SecondType
{
  private MyType _value;

  // Conversion operator.
  public static implicit operator MyType(SecondType t)
  {
    return t._value;
  }
}

object c = Factory.GetObject();

// Version one:
// o is SecondType:
MyType t = o as MyType; //Fails. o is not MyType

if (t != null)
{
  // work with t, it's a MyType.
}
else 
{
  // report the failure.
}

// Version two:
try
{
  MyType t1;
  t1 = (MyType)o; // Fails. o is not MyType
}
catch (InvalidCastException)
{
  // report the conversion failure.
}
{% endhighlight %}
这两种写法都无法完成类型转换。你也许觉得第二种写法可以完成类型转换，因为强制类型转换操作会把由用户所定义的转换逻辑也考虑进去。没错，确实会考虑进去，只不过**它针对的是源对象的编译期类型**，而不是实际类型。

要想把对象o从SecondType强制类型转换为MyType，可以将代码改成下面这个样子：

{% highlight csharp %}
object c = Factory.GetObject();

// Version three:
// 重点是这一句，st编译期类型是SecondType因此会调用SecondType定义的转换函数
SecondType st = o as SecondType; 
try
{
  MyType t;
  t = (MyType)st; // S
}
catch (InvalidCastException)
{
  // report the conversion failure.
}
{% endhighlight %}

由于as在转换失败是返回null，因此在值类型（比如：int）处理时无法使用as，此时可以使用可为空类型即可（比如：int?）

{% highlight csharp %}
foreach (MyType item in collection)
{
  item.DoStuff();
}
{% endhighlight %}
foreach语句需要同时应对值类型与引用类型，而这种采用cast的类型转换方式使得它在处理这两种类型时，可以展示出相同的行为。但是要注意，由于是通过cast方式来转换类型的，因此可能抛出InvalidCastException异常。