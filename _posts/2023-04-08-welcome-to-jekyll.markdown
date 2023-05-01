---
layout: post
title:  "Welcome to Jekyll!"
date:   2023-04-08 03:17:17 -0700
categories: jekyll update
tag: jekyll
---
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


本地调试命令 
{% highlight shell %}
bundler exec jekyll serve
{% endhighlight %}

URL配置，单独使用{post.url}，会导致url中不携带baseurl，此时可以通过absolute_url或relative_url的方式导入
{% highlight html %}
{% raw %}
<a href="{{ post.url | absolute_url }}">Post</a>
<a href="{{ "foo" | relative_url }}">Foo</a>
{% endraw %}
{% endhighlight %}

参考：

[minima主题拓展]

[Jekyll style guide]

[Jekyll style guide]: https://ben.balter.com/jekyll-style-guide/config/
[minima主题拓展]: https://puppylpg.github.io/2019/11/23/minima-customize/