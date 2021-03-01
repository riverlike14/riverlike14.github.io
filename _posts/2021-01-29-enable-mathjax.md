$$1+2=3$$

깃허브 블로그를 시작하면서 맨 처음 마주친 장벽은 수식 입력이다. `$`a + b = c`$`를 입력했는데
> $ a + b = c $

이게 안나온다... 본 블로그에서는 `_includes/head.html`에 다음을 입력했더니 해결이 됐다.

```html
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [ ['$$', '$$'], ["\\[", "\\]"] ],
    processEscapes: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
  }
});
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
```
