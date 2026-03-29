# Template Refs {#template-refs}

Mô hình kết xuất khai báo của Vue giúp bạn không phải đụng trực tiếp tới phần lớn thao tác DOM. Tuy vậy, vẫn có lúc ta cần truy cập thẳng vào phần tử DOM bên dưới. Để làm việc này, ta có thể dùng thuộc tính đặc biệt `ref`:

```vue-html
<input ref="input">
```

`ref` là một thuộc tính đặc biệt, khá giống với thuộc tính `key` đã nói tới trong chương `v-for`. Nó cho phép ta lấy tham chiếu trực tiếp tới một phần tử DOM cụ thể hoặc instance của component con sau khi nó được mount. Điều này hữu ích khi bạn muốn, chẳng hạn, tự động focus vào một ô input lúc component vừa mount, hoặc khởi tạo một thư viện bên thứ ba trên một phần tử.

## Truy cập ref {#accessing-the-refs}

<div class="composition-api">

Để lấy tham chiếu bằng Composition API, ta có thể dùng helper [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" />:

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

// đối số đầu tiên phải trùng với giá trị ref trong template
const input = useTemplateRef('my-input')

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

Khi dùng TypeScript, hỗ trợ IDE của Vue và `vue-tsc` sẽ tự suy ra kiểu của `input.value` dựa trên phần tử hoặc component mà thuộc tính `ref` tương ứng đang gắn vào.

<details>
<summary>Cách dùng trước phiên bản 3.5</summary>

Ở các phiên bản trước 3.5, khi `useTemplateRef()` chưa có, ta cần khai báo một ref có tên trùng với giá trị của thuộc tính ref trong template:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// khai báo ref để giữ tham chiếu tới phần tử
// tên phải trùng với giá trị ref trong template
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

Nếu không dùng `<script setup>`, nhớ trả ref ra từ `setup()`:

```js{6}
export default {
  setup() {
    const input = ref(null)
    // ...
    return {
      input
    }
  }
}
```

</details>

</div>
<div class="options-api">

Ref tạo ra sẽ được đưa vào `this.$refs`:

```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input" />
</template>
```

</div>

Lưu ý là bạn chỉ có thể truy cập ref **sau khi component đã được mount.** Nếu bạn thử truy cập <span class="options-api">`$refs.input`</span><span class="composition-api">`input`</span> trong một biểu thức template, nó sẽ là <span class="options-api">`undefined`</span><span class="composition-api">`null`</span> ở lần render đầu tiên. Lý do là vì phần tử đó chưa tồn tại trước lần render đầu.

<div class="composition-api">

Nếu bạn muốn theo dõi sự thay đổi của một template ref, hãy nhớ xử lý trường hợp ref có giá trị `null`:

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // chưa được mount, hoặc phần tử đã bị unmount (ví dụ bởi v-if)
  }
})
```

Xem thêm: [Khai báo kiểu cho Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Ref trên component {#ref-on-component}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Nếu muốn, bạn có thể bỏ qua và quay lại sau.

`ref` cũng có thể dùng trên một component con. Khi đó, tham chiếu nhận được sẽ là instance của component:

<div class="composition-api">

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // childRef.value sẽ chứa instance của <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

<details>
<summary>Cách dùng trước phiên bản 3.5</summary>

```vue
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value sẽ chứa instance của <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

</details>

</div>
<div class="options-api">

```vue
<script>
import Child from './Child.vue'

export default {
  components: {
    Child
  },
  mounted() {
    // this.$refs.child sẽ chứa instance của <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>

<span class="composition-api">Nếu component con đang dùng Options API hoặc không dùng `<script setup>`, thì</span><span class="options-api">Thì</span> instance được tham chiếu sẽ giống hệt `this` của component con. Điều đó có nghĩa là component cha sẽ truy cập được toàn bộ property và method của component con. Cách này rất dễ làm cho chi tiết cài đặt giữa cha và con bị gắn chặt với nhau, nên chỉ nên dùng component ref khi thật sự cần. Trong đa số trường hợp, bạn nên ưu tiên dùng props và emit tiêu chuẩn để component cha và con giao tiếp với nhau.

<div class="composition-api">

Một ngoại lệ ở đây là các component dùng `<script setup>` sẽ **được giữ riêng tư theo mặc định**: component cha khi tham chiếu tới một component con dùng `<script setup>` sẽ không truy cập được gì cả, trừ khi component con chủ động công khai giao diện công khai bằng macro `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Compiler macro như defineExpose không cần import
defineExpose({
  a,
  b
})
</script>
```

Khi component cha lấy instance của component này qua template ref, instance nhận được sẽ có dạng `{ a: number, b: number }` (ref sẽ được tự động mở bọc giống như trên instance thông thường).

Lưu ý `defineExpose` phải được gọi trước mọi thao tác `await`. Nếu không, những property và method được expose sau `await` sẽ không thể truy cập được.

Xem thêm: [Khai báo kiểu cho Template Refs của Component](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Option `expose` có thể dùng để giới hạn quyền truy cập vào instance của component con:

```js
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}
```

Trong ví dụ trên, component cha khi tham chiếu tới component này qua template ref sẽ chỉ truy cập được `publicData` và `publicMethod`.

</div>

## Ref bên trong `v-for` {#refs-inside-v-for}

> Cần Vue 3.5 trở lên

<div class="composition-api">

Khi dùng `ref` bên trong `v-for`, ref tương ứng nên chứa một giá trị mảng. Sau khi mount xong, mảng này sẽ được điền các phần tử tương ứng:

```vue
<script setup>
import { ref, useTemplateRef, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = useTemplateRef('items')

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9UsluwjAQ/ZWRLwQpDepyQoDUIg6t1EWUW91DFAZq6tiWF4oU5d87dtgqVRyyzLw3b+aN3bB7Y4ptQDZkI1dZYTw49MFMuBK10dZDAxZXOQSHC6yNLD3OY6zVsw7K4xJaWFldQ49UelxxVWnlPEhBr3GszT6uc7jJ4fazf4KFx5p0HFH+Kme9CLle4h6bZFkfxhNouAIoJVqfHQSKbSkDFnVpMhEpovC481NNVcr3SaWlZzTovJErCqgydaMIYBRk+tKfFLC9Wmk75iyqg1DJBWfRxT7pONvTAZom2YC23QsMpOg0B0l0NDh2YjnzjpyvxLrYOK1o3ckLZ5WujSBHr8YL2gxnw85lxEop9c9TynkbMD/kqy+svv/Jb9wu5jh7s+jQbpGzI+ZLu0byEuHZ+wvt6Ays9TJIYl8A5+i0DHHGjvYQ1JLGPuOlaR/TpRFqvXCzHR2BO5iKg0Zmm/ic0W2ZXrB+Gve2uEt1dJKs/QXbwePE)

<details>
<summary>Cách dùng trước phiên bản 3.5</summary>

Ở các phiên bản trước 3.5, khi `useTemplateRef()` chưa có, ta cần khai báo một ref có tên trùng với giá trị của thuộc tính ref trong template. Ref này cũng nên chứa sẵn một giá trị mảng:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```

</details>

</div>
<div class="options-api">

Khi dùng `ref` bên trong `v-for`, giá trị ref nhận được sẽ là một mảng chứa các phần tử tương ứng:

```vue
<script>
export default {
  data() {
    return {
      list: [
        /* ... */
      ]
    }
  },
  mounted() {
    console.log(this.$refs.items)
  }
}
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNpFjk0KwjAQha/yCC4Uaou6kyp4DuOi2KkGYhKSiQildzdNa4WQmTc/37xeXJwr35HEUdTh7pXjszT0cdYzWuqaqBm9NEDbcLPeTDngiaM3PwVoFfiI667AvsDhNpWHMQzF+L9sNEztH3C3JlhNpbaPNT9VKFeeulAqplfY5D1p0qurxVQSqel0w5QUUEedY8q0wnvbWX+SYgRAmWxIiuSzm4tBinkc6HvkuSE7TIBKq4lZZWhdLZfE8AWp4l3T)

</div>

Cần lưu ý là mảng ref **không** đảm bảo cùng thứ tự với mảng nguồn.

## Function Ref {#function-refs}

Thay vì dùng một key dạng chuỗi, thuộc tính `ref` cũng có thể được gắn với một hàm. Hàm này sẽ được gọi mỗi khi component cập nhật, và cho bạn toàn quyền quyết định nơi lưu tham chiếu tới phần tử. Hàm sẽ nhận tham chiếu phần tử làm đối số đầu tiên:

```vue-html
<input :ref="(el) => { /* gán el vào một property hoặc ref */ }">
```

Lưu ý là ở đây ta dùng ràng buộc động `:ref` để có thể truyền vào một hàm thay vì một chuỗi tên ref. Khi phần tử bị unmount, đối số truyền vào sẽ là `null`. Tất nhiên, bạn cũng có thể dùng một method thay cho hàm inline.
