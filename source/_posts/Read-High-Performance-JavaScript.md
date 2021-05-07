---
title: 《高性能JAVASCRIPT》读书笔记
date: 2013-06-17 22:22:22
categories: 
  - 自我提升
	- 读书笔记
---
> 作者：李旭光
> 引用请标明出处


# 前言

最近读了《高性能JAVASCRIPT》，下面将书中觉得有用的内容进行摘抄，放在下方。
# 如何从小事提升JAVASCRIPT性能。
1. 将`<script>`标签写在`</body>`之前——将脚本放在底部。
2. 尽量少的`<script>`标签，减少页面中的外链数量，减少请求次数。
3. 使用打包工具如：Yahoo！combo handler
4. 使用动态延迟加载技术如：LazyLoad类库,LABjs
```
// js代码
<script type="text/javascript" src="lazyload-min.js"></script>
<script type="text/javascript">
  LazyLoad.js([],function(){
    Application.init();
  })
</script>
```
5. 在javascript中存储位置十分重要，尽量用局部变量代替对象成员的访问。
6. 集合变数组提高查询效率
```
// js代码
function toArray(coll){
  for(var i = 0, a=[], len=coll.length; i<len; i++){
    a[i]=col[i];
  }
  return a;
}
```
7. 使用局部变量缓存访问多次的成员
当便利一个集合时，首要优化原则是把集合存储在局部变量中，并把length缓存在循环外部，然后使用局部变量访问这些需要多次访问的元素。
8. 使用原生DOM方法querySelectorAll（）遍历查找元素。

9. 让元素脱离动画流，页面重排次数越多，程序响应越慢，要尽量减少重拍
方法：
  9.1. 使用绝对定位使元素脱离文档流

10. IE：hover
在大量元素使用：hover这个伪css选择器时，程序会降低响应速率，在大表格或列表的情况下