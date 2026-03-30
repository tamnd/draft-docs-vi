# Ràng buộc thuộc tính {#attribute-bindings}

Trong Vue, mustache chỉ dùng cho nội suy text. Để bind một thuộc tính vào giá trị động, chúng ta dùng directive `v-bind`:

```vue-html
<div v-bind:id="dynamicId"></div>
```

Một **directive** là thuộc tính đặc biệt bắt đầu bằng tiền tố `v-`. Chúng là một phần của cú pháp template Vue. Tương tự nội suy text, giá trị directive là biểu thức JavaScript có quyền truy cập state của component. Chi tiết đầy đủ về `v-bind` và cú pháp directive được thảo luận trong <a target="_blank" href="/guide/essentials/template-syntax.html">Hướng dẫn - Cú pháp Template</a>.

Phần sau dấu hai chấm (`:id`) là "đối số" của directive. Ở đây, thuộc tính `id` của element sẽ được đồng bộ với thuộc tính `dynamicId` từ state của component.

Vì `v-bind` được dùng rất thường xuyên, nó có cú pháp viết tắt riêng:

```vue-html
<div :id="dynamicId"></div>
```

Bây giờ, hãy thử thêm binding `class` động vào `<h1>`, dùng <span class="options-api">data property</span><span class="composition-api">ref</span> `titleClass` làm giá trị của nó. Nếu được bind đúng, text sẽ chuyển sang màu đỏ.
