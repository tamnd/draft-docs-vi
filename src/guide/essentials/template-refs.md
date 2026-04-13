# Template Refs {#template-refs}

Trong khi mô hình render khai báo (declarative rendering) của Vue giúp bạn tránh phải thao tác trực tiếp với DOM trong hầu hết các trường hợp, vẫn sẽ có những tình huống chúng ta cần truy cập trực tiếp vào các phần tử DOM bên dưới. Để làm điều này, chúng ta có thể dùng attribute đặc biệt `ref`:

```vue-html
<input ref="input">
```

`ref` là một attribute đặc biệt, tương tự như attribute `key` đã được đề cập trong chương `v-for`. Nó cho phép chúng ta lấy được một tham chiếu trực tiếp tới một phần tử DOM cụ thể hoặc instance của component con sau khi nó được mount. Điều này hữu ích khi bạn muốn, ví dụ, focus một input bằng code khi component mount, hoặc khởi tạo một thư viện bên thứ ba trên một phần tử.

## Truy cập Refs {#accessing-the-refs}

<div class="composition-api">

Để lấy tham chiếu với Composition API, chúng ta có thể dùng helper `useTemplateRef()`:

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

// tham số đầu tiên phải trùng với giá trị ref trong template
const input = useTemplateRef('my-input')

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

Khi dùng TypeScript, IDE của Vue và `vue-tsc` sẽ tự động suy luận kiểu của `input.value` dựa trên phần tử hoặc component tương ứng với attribute `ref`.

<details>
<summary>Cách dùng trước phiên bản 3.5</summary>

Trong các phiên bản trước 3.5 khi chưa có `useTemplateRef()`, chúng ta cần khai báo một ref với tên trùng với giá trị của attribute `ref` trong template:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// khai báo ref để giữ tham chiếu phần tử
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

Nếu không dùng `<script setup>`, cần return ref từ `setup()`:

```js
export default {
  setup() {
    const input = ref(null)
    return {
      input
    }
  }
}
```

</details>

</div>
<div class="options-api">

Ref kết quả sẽ được expose trên `this.$refs`:

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

Lưu ý rằng bạn chỉ có thể truy cập ref **sau khi component đã được mount.** Nếu bạn cố truy cập <span class="options-api">`$refs.input`</span><span class="composition-api">`input`</span> trong biểu thức template, nó sẽ là <span class="options-api">`undefined`</span><span class="composition-api">`null`</span> ở lần render đầu tiên. Điều này là vì phần tử chưa tồn tại cho đến sau lần render đầu tiên.

<div class="composition-api">

Nếu bạn muốn theo dõi thay đổi của template ref, hãy đảm bảo xử lý trường hợp ref có giá trị `null`:

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // chưa mount, hoặc phần tử đã bị unmount (ví dụ bởi v-if)
  }
})
```

> Xem thêm: [Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Ref trên Component {#ref-on-component}

Phần này giả định bạn đã biết về Components.

`ref` cũng có thể dùng trên component con. Trong trường hợp này, tham chiếu sẽ là instance của component:

<div class="composition-api">

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // childRef.value sẽ là instance của <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

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
    // this.$refs.child sẽ là instance của <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>

Instance được tham chiếu sẽ giống với `this` của component con, nghĩa là component cha có thể truy cập mọi property và method của component con. Điều này có thể dẫn đến coupling chặt giữa các component, vì vậy chỉ nên dùng ref khi thực sự cần thiết. Trong đa số trường hợp, nên dùng props và emit để giao tiếp giữa parent và child.

<div class="composition-api">

Một ngoại lệ là component dùng `<script setup>` mặc định là private. Component cha sẽ không truy cập được gì trừ khi component con expose API qua `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

Khi component cha lấy instance qua ref, object nhận được sẽ có dạng `{ a: number, b: number }`.

Lưu ý `defineExpose` phải được gọi trước bất kỳ thao tác await nào.

> Xem thêm: [Typing Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>

<div class="options-api">

Option `expose` có thể dùng để giới hạn truy cập:

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
    publicMethod() {},
    privateMethod() {}
  }
}
```

Component cha chỉ truy cập được `publicData` và `publicMethod`.

</div>

## Refs trong `v-for` {#refs-inside-v-for}

> Yêu cầu v3.5 trở lên

<div class="composition-api">

Khi dùng `ref` trong `v-for`, ref sẽ là một array chứa các phần tử sau khi mount:

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

[Thử trong Playground](https://play.vuejs.org/#eNp9UsluwjAQ/ZWRLwQpDepyQoDUIg6t1EWUW91DFAZq6tiWF4oU5d87dtgqVRyyzLw3b+aN3bB7Y4ptQDZkI1dZYTw49MFMuBK10dZDAxZXOQSHC6yNLD3OY6zVsw7K4xJaWFldQ49UelxxVWnlPEhBr3GszT6uc7jJ4fazf4KFx5p0HFH+Kme9CLle4h6bZFkfxhNouAIoJVqfHQSKbSkDFnVpMhEpovC481NNVcr3SaWlZzTovJErCqgydaMIYBRk+tKfFLC9Wmk75iyqg1DJBWfRxT7pONvTAZom2YC23QsMpOg0B0l0NDh2YjnzjpyvxLrYOK1o3ckLZ5WujSBHr8YL2gxnw85lxEop9c9TynkbMD/kqy+svv/Jb9wu5jh7s+jQbpGzI+ZLu0byEuHZ+wvt6Ays9TJIYl8A5+i0DHHGjvYQ1JLGPuOlaR/TpRFqvXCzHR2BO5iKg0Zmm/ic0W2ZXrB+Gve2uEt1dJKs/QXbwePE)

<details>
<summary>Cách dùng trước v3.5</summary>

Trong các phiên bản trước 3.5 khi chưa có `useTemplateRef()`, chúng ta cần khai báo một ref với tên trùng với giá trị của attribute `ref` trong template. Ref này cũng cần chứa giá trị array:

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

Khi dùng `ref` trong `v-for`, giá trị ref sẽ là một array:

```vue
<script>
export default {
  data() {
    return {
      list: []
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

</div>

Lưu ý rằng thứ tự trong array ref **không đảm bảo giống thứ tự của source array.**

## Function Refs {#function-refs}

Thay vì dùng string, attribute `ref` cũng có thể bind với một function. Function này sẽ được gọi mỗi lần component update và cho phép bạn toàn quyền quyết định nơi lưu tham chiếu phần tử. Function nhận phần tử làm tham số đầu tiên:

```vue-html
<input :ref="(el) => { /* gán el vào property hoặc ref */ }">
```

Chúng ta dùng `:ref` động để truyền function thay vì string. Khi phần tử bị unmount, tham số sẽ là `null`. Bạn cũng có thể dùng method thay cho inline function.

