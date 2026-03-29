# Thuộc tính Computed {#computed-property}

Hãy tiếp tục xây dựng dựa trên danh sách todo từ bước trước. Ở đây, chúng ta đã thêm chức năng toggle cho mỗi todo. Điều này được thực hiện bằng cách thêm thuộc tính `done` vào mỗi todo object, và dùng `v-model` để bind nó vào checkbox:

```vue-html{2}
<li v-for="todo in todos">
  <input type="checkbox" v-model="todo.done">
  ...
</li>
```

Cải tiến tiếp theo chúng ta có thể thêm là có khả năng ẩn các todo đã hoàn thành. Chúng ta đã có nút toggle state `hideCompleted`. Nhưng làm thế nào để render các list item khác nhau dựa trên state đó?

<div class="options-api">

Giới thiệu <a target="_blank" href="/guide/essentials/computed.html">thuộc tính computed</a>. Chúng ta có thể khai báo một thuộc tính được tính toán reactive từ các thuộc tính khác bằng tùy chọn `computed`:

<div class="sfc">

```js
export default {
  // ...
  computed: {
    filteredTodos() {
      // trả về todos đã lọc dựa trên `this.hideCompleted`
    }
  }
}
```

</div>
<div class="html">

```js
createApp({
  // ...
  computed: {
    filteredTodos() {
      // trả về todos đã lọc dựa trên `this.hideCompleted`
    }
  }
})
```

</div>

</div>
<div class="composition-api">

Giới thiệu <a target="_blank" href="/guide/essentials/computed.html">`computed()`</a>. Chúng ta có thể tạo một computed ref tính toán `.value` của nó dựa trên các nguồn dữ liệu reactive khác:

<div class="sfc">

```js{8-11}
import { ref, computed } from 'vue'

const hideCompleted = ref(false)
const todos = ref([
  /* ... */
])

const filteredTodos = computed(() => {
  // trả về todos đã lọc dựa trên
  // `todos.value` & `hideCompleted.value`
})
```

</div>
<div class="html">

```js{10-13}
import { createApp, ref, computed } from 'vue'

createApp({
  setup() {
    const hideCompleted = ref(false)
    const todos = ref([
      /* ... */
    ])

    const filteredTodos = computed(() => {
      // trả về todos đã lọc dựa trên
      // `todos.value` & `hideCompleted.value`
    })

    return {
      // ...
    }
  }
})
```

</div>

</div>

```diff
- <li v-for="todo in todos">
+ <li v-for="todo in filteredTodos">
```

Thuộc tính computed theo dõi các reactive state khác được dùng trong quá trình tính toán của nó như các dependency. Nó cache kết quả và tự động cập nhật khi các dependency thay đổi.

Bây giờ, hãy thử thêm thuộc tính computed `filteredTodos` và triển khai logic tính toán của nó! Nếu được triển khai đúng, đánh dấu hoàn thành một todo khi đang ẩn các item đã hoàn thành sẽ lập tức ẩn nó đi.
