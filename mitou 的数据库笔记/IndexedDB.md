# IndexedDB

IndexedDB（Indexed Database）是一种浏览器端的数据库，它允许网页在客户端存储结构化数据。、

与传统的 Cookie 和 LocalStorage 不同，IndexedDB 提供了更强大和灵活的存储能力，使得网页可以存储大量数据，支持复杂的查询。

- 结构化数据存储

	IndexedDB 可以存储复杂的 JavaScript 对象，而不仅限于简单的键值对。

- 异步操作

	IndexedDB 的操作都是异步执行的，由事件操作。

- 可离线

	退出浏览器后，IndexedDB 的数据仍然保留。

## 对比

- Cookie
	- 只能在同一 session 内存在。
	- 单个 Cookie 大小通常限制在几 KB，每个域名下的 Cookie 数量也有限制。
- LocalStorage
	- 可跨 session 。
	- 可以存储大量的字符串数据（通常可达到几百 KB）。
- IndexedDB
	- 可离线存储，退出浏览器后数据仍然保留。
	- 可以存储数百 MB 的数据。