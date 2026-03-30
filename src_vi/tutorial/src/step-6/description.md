# Kết xuất có điều kiện {#conditional-rendering}

Chúng ta có thể dùng directive `v-if` để render một element có điều kiện:

```vue-html
<h1 v-if="awesome">Vue thật tuyệt vời!</h1>
```

`<h1>` này sẽ chỉ được render nếu giá trị của `awesome` là [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). Nếu `awesome` thay đổi thành giá trị [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy), nó sẽ bị xóa khỏi DOM.

Chúng ta cũng có thể dùng `v-else` và `v-else-if` để biểu thị các nhánh khác của điều kiện:

```vue-html
<h1 v-if="awesome">Vue thật tuyệt vời!</h1>
<h1 v-else>Ồ không 😢</h1>
```

Hiện tại, demo đang hiển thị cả hai `<h1>` cùng lúc, và nút không làm gì. Hãy thêm directive `v-if` và `v-else` vào chúng, và triển khai method `toggle()` để chúng ta có thể dùng nút để chuyển đổi giữa chúng.

Chi tiết thêm về `v-if`: <a target="_blank" href="/guide/essentials/conditional.html">Hướng dẫn - Kết xuất có điều kiện</a>
