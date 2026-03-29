# Event Listener {#event-listeners}

Chúng ta có thể lắng nghe sự kiện DOM bằng directive `v-on`:

```vue-html
<button v-on:click="increment">{{ count }}</button>
```

Do được dùng thường xuyên, `v-on` cũng có cú pháp viết tắt:

```vue-html
<button @click="increment">{{ count }}</button>
```

<div class="options-api">

Ở đây, `increment` tham chiếu một hàm được khai báo bằng tùy chọn `methods`:

<div class="sfc">

```js{7-12}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      // cập nhật state component
      this.count++
    }
  }
}
```

</div>
<div class="html">

```js{7-12}
createApp({
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      // cập nhật state component
      this.count++
    }
  }
})
```

</div>

Bên trong method, chúng ta có thể truy cập component instance bằng `this`. Component instance expose các data property được khai báo bởi `data`. Chúng ta có thể cập nhật state component bằng cách thay đổi các thuộc tính này.

</div>

<div class="composition-api">

<div class="sfc">

Ở đây, `increment` tham chiếu một hàm được khai báo trong `<script setup>`:

```vue{6-9}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  // cập nhật state component
  count.value++
}
</script>
```

</div>

<div class="html">

Ở đây, `increment` tham chiếu một method trong object được trả về từ `setup()`:

```js{$}
setup() {
  const count = ref(0)

  function increment(e) {
    // cập nhật state component
    count.value++
  }

  return {
    count,
    increment
  }
}
```

</div>

Bên trong hàm, chúng ta có thể cập nhật state component bằng cách thay đổi ref.

</div>

Event handler cũng có thể dùng biểu thức inline, và có thể đơn giản hóa các tác vụ phổ biến bằng modifier. Các chi tiết này được đề cập trong <a target="_blank" href="/guide/essentials/event-handling.html">Hướng dẫn - Xử lý Sự kiện</a>.

Bây giờ, hãy thử tự triển khai <span class="options-api">method</span><span class="composition-api">hàm</span> `increment` và bind nó vào nút bằng `v-on`.
