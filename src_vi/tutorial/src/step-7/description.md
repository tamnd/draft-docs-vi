# Kết xuất danh sách {#list-rendering}

Chúng ta có thể dùng directive `v-for` để render danh sách các element dựa trên mảng nguồn:

```vue-html
<ul>
  <li v-for="todo in todos" :key="todo.id">
    {{ todo.text }}
  </li>
</ul>
```

Ở đây `todo` là biến cục bộ đại diện cho phần tử mảng đang được lặp lại. Nó chỉ có thể truy cập trên hoặc bên trong element `v-for`, tương tự phạm vi hàm.

Chú ý cách chúng ta cũng gán cho mỗi todo object một `id` duy nhất, và bind nó làm <a target="_blank" href="/api/built-in-special-attributes.html#key">thuộc tính đặc biệt `key`</a> cho mỗi `<li>`. `key` cho phép Vue di chuyển chính xác từng `<li>` để khớp với vị trí của object tương ứng trong mảng.

Có hai cách để cập nhật danh sách:

1. Gọi [mutating method](https://stackoverflow.com/questions/9009879/which-javascript-array-functions-are-mutating) trên mảng nguồn:

   <div class="composition-api">

   ```js
   todos.value.push(newTodo)
   ```

     </div>
     <div class="options-api">

   ```js
   this.todos.push(newTodo)
   ```

   </div>

2. Thay thế mảng bằng một mảng mới:

   <div class="composition-api">

   ```js
   todos.value = todos.value.filter(/* ... */)
   ```

     </div>
     <div class="options-api">

   ```js
   this.todos = this.todos.filter(/* ... */)
   ```

   </div>

Chúng ta đã có sẵn một danh sách todo đơn giản - hãy thử triển khai logic cho các method `addTodo()` và `removeTodo()` để làm cho nó hoạt động!

Chi tiết thêm về `v-for`: <a target="_blank" href="/guide/essentials/list.html">Hướng dẫn - Kết xuất danh sách</a>
