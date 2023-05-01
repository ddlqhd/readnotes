---
layout: post
title:  "[Effective C#] 第5条：用FormattableString取代专门为特定区域而写的字符串"
date:   2023-05-02 00:11:00 +0800
categories: c#
tag: Effective C#
---

内插字符串的一项设计目标是想令开发者能够将其与现有的string类顺畅地结合起来，同时，还必须能够应对全球各地的多种语言。

C#的字符串机制会把内插字符串的解读结果隐式地转换成string或FormattableString。FormattableString可以处理多语言的格式化差异，而string不行。
{% highlight csharp %}
string first = 
  $"It's the {DateTime.Now.Day} of the {DateTime.Now.Month} month"

Formattab1eString second =
  $"It's the {DateTime.Now.Day} of the {DateTime.Now.Month} month"
{% endhighlight %}
编译器会根据应该输出的信息所具有的运行期类型来产生不同的程序码，其中，用来创建字符串的那一部分程序码会根据执行该程序的计算机当前所在的区域来设定字符串的格式。如果在美国运行代码，那么double值的整数与小数之间会用句点（.）来分隔，如果在欧洲国家运行，那么分隔符则是逗号（，）。

开发者可以利用编译器的类型判定机制来直接生成string或Formattable-String，也可以编写方法，把内插字符串的解读结果转换成适用于某个地区的字符串。比方说下面这两个方法就可以把FormattableString转换成针对特定语言与特定地区的string。
{% highlight csharp %}
public static string ToGerman(FormattableString src)
{
  return string.Format(null, System.Globalization.Culturelnfo.
    CreateSpecificCu1ture("de-de"),
    src.Format,
    src.GetArguments());
}

public static string ToFrenchCanada(FormattableString src)
{
    return string.Format(null, System.Globalization.Culturelnfo.
    CreateSpecificCu1ture("fr-CA"),
    src.Format,
    src.GetArguments());
}
{% endhighlight %}

首先要注意，不要给这些方法编写以string为参数的重载版本，否则，编译器在面对既可以选string版本又可以选FormattableString版本的情况下，会创建出生成string的程序码，进而调用以string为参数的那个版本。

如果程序只是针对当前区域而生成文本，那么直接使用内插字符串就够了，这样反而可以避免多余的操作。反之，如果需要针对特定的地区及语言来生成字符串，那么就必须根据内插字符串的解读结果来创建FormattableString，并将其转换成适用于该地区及该语言的字符串。