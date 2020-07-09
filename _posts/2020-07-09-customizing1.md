--- 
title: 블로그 customizing(1)
---

# 커스터마이징 1편

## 1. 글 업데이트 된 날짜로 바꾸기

**_includes/archive-single.html**
``` html
    <!-- {% if post.read_time %}
      <p class="page__meta"><i class="far fa-clock" aria-hidden="true"></i> {% include read-time.html %}</p>
    {% endif %} -->
    {% if post.date %}
      <p class="page__meta"><i class="far fa-fw fa-calendar-alt" aria-hidden="true"></i> {{ post.date | date: "%B %d %Y" }}</p>
    {% endif %}
```

**_layouts/single.html**
``` html
<!-- <p class="page__meta"><i class="far fa-clock" aria-hidden="true"></i> {% include read-time.html %}</p> -->
<p class="page__date"><i class="fas fa-fw fa-calendar-alt" aria-hidden="true"></i> {{ site.data.ui-text[site.locale].date_label | default: "Updated:" }} <time datetime="{{ page.date | date_to_xmlschema }}">{{ page.date | date: "%B %d, %Y" }}</time></p>
```


## 2. 포스트 글 밑줄 없애기
**_sass/minimal-mistakes/_base.scss**
``` html
  text-decoration: none; <!--이거 추가-->
  &:focus {
    @extend %tab-focus;
  }
```

