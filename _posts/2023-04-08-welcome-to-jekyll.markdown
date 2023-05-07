---
layout: post
title:  "Welcome to Jekyll!"
date:   2023-04-08 03:17:17 -0700
categories: jekyll update
tag: jekyll
---
# 使用说明
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

[Jekyll style guide]

[Jekyll style guide]: https://ben.balter.com/jekyll-style-guide/config/

# 本地调试命令 
{% highlight shell %}
bundler exec jekyll serve
{% endhighlight %}

# URL配置
单独使用{post.url}，会导致url中不携带baseurl，此时可以通过absolute_url或relative_url的方式导入
{% highlight html %}
{% raw %}
<a href="{{ post.url | absolute_url }}">Post</a>
<a href="{{ "foo" | relative_url }}">Foo</a>
{% endraw %}
{% endhighlight %}

参考：
[minima主题拓展]

[minima主题拓展]: https://puppylpg.github.io/2019/11/23/minima-customize/
# Post格式说明

|  字段   | 说明  |
|  ----  | ----  |
| layout | 布局模板，当前模板里，固定为post |
| title  | 文章的标题，注意需要使用""将标题包起来 |
| date  | 日期，页面渲染时根据该日期对post进行排序，注意不能是未来的时间，否则不会现实 |
| categories | 类别，在当前的模板配置中，此类别会显示在渲染的页面路径中，类似于文件夹的概念，取值时最好不要有空格 |
| tag  | 分类标签，渲染的Tags页面中根据post的tag来对文章归类显示 |

举例：
{% highlight html %}
layout: post
title:  "[Effective C#] 第1条：优先使用隐式类型的局部变量"
date:   2023-04-22 22:09:17 +0800
categories: "effective-cs"
tag: Effective C#

页面地址为：
http://localhost:4000/readnotes/effective-cs/2023/04/22/effective-cs-section1.html
{% endhighlight %}
