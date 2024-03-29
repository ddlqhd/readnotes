---
layout: post
title:  "[Effective C#] 第6条：不要用表示符号名称的硬字符串来调用API"
date:   2023-05-07 09:14:00 +0800
categories: effective-cs
tag: Effective C#
---

**核心观点**：使用nameof获取变量名称，而不是用硬编码字符串的方式直接写变量名称。

**好处是**：使用nameof后，其保留了对变量的应用，在使用IDE重命名或者静态检查是能够自动修改或校验变量名称的合法性，而直接通过字符串硬编码写变量名则无法自动检查。

**注意的点**：
nameof（）会根据符号求出表示该符号名称的字符串，这个符号可以指类型、变量、接口及命名空间。符号既可以写成非限定的形式，也可以写成完全限定的形式。针对泛型类来使用nameof时，会受到一些限制，因为nameof只支持封闭的泛型类，也就是说，开发者必须把所有的类型参数全都指定出来。

即使变量是用完全限定的方式传给nameof的，它也依然会返回局部名称。例如**把System.Int.MaxValue传给它，会得到MaxValue**。

