---
title: 自己动手实现系列 ---- 事件代理
date: 2020-03-24 22:40:22
categories: 
	- 源码原理
	- 自己动手实现系列
tags: 
	- 自己动手实现系列
---
# 实现

``` js
<ul id="color-list">
  <li>red</li>
  <li>yellow</li>
  <li>blue</li>
  <li>green</li>
  <li>black</li>
  <li>white</li>
</ul>

<script>
  (function () {
    var color_list = document.getElementById('color-list');
    color_list.addEventListener('click', showColor, true);
    function showColor(e) {
      var x = e.target;
      if (x.nodeName.toLowerCase() === 'li') {
        alert(x.innerHTML);
      }
    }
  })();
</script>
```