==SPA（单页应用，Single-page application）是只加载一个单独网页的 Web 应用的实现==。当需要显示不同的内容时，它通过 JavaScript API（例如 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 和 [Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)）更新主体内容。

==SPA 让用户在访问网站时无需加载新的页面，从而获得性能提升和动态的体验，但也会相应地丧失诸如 SEO（搜索引擎优化）的优势==，同时需要更多精力维护状态、实现导航以及做一些有意义的性能监控。