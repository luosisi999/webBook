### 1、axios和ajax的区别及优缺点
[博客](https://www.cnblogs.com/yjf713/p/13341376.html)
- ajax是基于XMLHttpRequest对象进行封装的技术，能实现页面无刷新就更新数据
    - 缺点：
        - ajax不能使用Back和history功能，即对浏览器机制的破坏。
        - 需要进入整个jquery，开销大
- Axios 是一个基于 Promise 的 HTTP 库，可以用在浏览器和 node.js 中。
- 执行get请求，有两种方式
```
// 第一种方式  将参数直接写在url中
axios.get('/getMainInfo?id=123')
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
// 第二种方式  将参数直接写在params中
axios.get('/getMainInfo', {
  params: {
    id: 123
  }
})
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```
- 执行post请求，注意执行post请求的入参，
不需要写在params字段中，这个地方要注意与get请求的第二种方式进行区别。
```
axios.post('/getMainInfo', {
  id: 123
})
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```