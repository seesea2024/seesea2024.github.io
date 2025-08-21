---
layout: post
---

## github pages如何支持分页

jekyll官方有支持github pages分页的插件jekyll-paginate. 官方参考[pagination](seesea2024.github.io localhost).配置起来不难，但中间有一些坑，假期折腾我一两天。


###编辑 Gemfile

```
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
   gem "jekyll-paginate"
end

```

###编辑 _config.yml
这里比较坑的地方是必须建立一个/blog(站点根目录下）和index.html. pagination 原理上是把index.html中从_posts目录下抓取的文件进行分页。

```
paginate: 10
paginate_path: "/blog/page:num/"

```

### 编辑 /blog/index.html
主要／blog目录下面 **一定** 要有index.html,否则分页功能无效。

```
{% raw %}
---
layout: default
---

{% include page.html %}
{% endraw %}
```

### 编辑 _includes/page.html

```
{% raw %}
<!-- This loops through the paginated posts -->
{% for post in paginator.posts %}
  <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
{% endfor %}

{% if paginator.total_pages > 1 %}
<div class="pagination">
  {% if paginator.previous_page %}
    <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">&laquo; Prev</a>
  {% else %}
    <span>&laquo; Prev</span>
  {% endif %}

  {% for page in (1..paginator.total_pages) %}
    {% if page == paginator.page %}
      <em>{{ page }}</em>
    {% elsif page == 1 %}
      <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">{{ page }}</a>
    {% else %}
      <a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page }}">{{ page }}</a>
    {% endif %}
  {% endfor %}

  {% if paginator.next_page %}
    <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">Next &raquo;</a>
  {% else %}
    <span>Next &raquo;</span>
  {% endif %}
</div>
{% endif %}
{% endraw %}
```
### 编辑 _includes/header.html
这个对应posts单个页面上返回列表页面的链接。因为列表页地址现在多了一个blog。

```
 <a class="site-title" href="{{ "/blog" | relative_url}}">{{ site.title | escape }}</a>
     
```


中间有jekyll版本冲突问题，通过```sudo bundle update```解决。good luck！
