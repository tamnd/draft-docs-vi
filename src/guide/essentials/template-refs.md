# Template Refs {#template-refs}

Mặc dù mô hình render khai báo của Vue giúp trừu tượng hóa hầu hết các thao tác DOM trực tiếp, vẫn có những trường hợp chúng ta cần truy cập trực tiếp vào các phần tử DOM bên dưới. Để làm điều này, chúng ta có thể sử dụng thuộc tính đặc biệt `ref`:

```vue-html
<input ref="input">
```

`ref` là một thuộc tính đặc biệt, tương tự thuộc tính `key` đã đề cập trong chương `v-for`. Nó cho phép chúng ta lấy tham chiếu trực tiếp đến một phần tử DOM cụ thể hoặc instance của component con sau khi được mount. Điều này hữu ích khi bạn muốn, chẳng hạn, lập trình cho input được focus khi component mount, hoặc khởi tạo một thư viện bên thứ ba trên một phần tử.

## Truy cập các Ref {#accessing-the-refs}

<div class="composition-api">

Để lấy tham chiếu bằng Composition API, chúng ta có thể dùng helper [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" />:

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

// the first argument must match the ref value in the template
const input = useTemplateRef('my-input')

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

Khi sử dụng TypeScript, hỗ trợ IDE của Vue và `vue-tsc` sẽ tự động suy luận kiểu của `input.value` dựa trên phần tử hoặc component mà thuộc tính `ref` tương ứng được dùng trên đó.

<details>
<summary>Usage before 3.5</summary>

In versions before 3.5 where `useTemplateRef()` was not introduced, we need to declare a ref with a name that matches the template ref attribute's value:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// declare a ref to hold the element reference
// the name must match template ref value
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

If not using `<script setup>`, make sure to also return the ref from `setup()`:

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

Ref kết quả được expose trên `this.$refs`:

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

Lưu ý rằng bạn chỉ có thể truy cập ref **sau khi component đã được mount.** Nếu bạn cố truy cập <span class="options-api">`$refs.input`</span><span class="composition-api">`input`</span> trong một biểu thức template, giá trị sẽ là <span class="options-api">`undefined`</span><span class="composition-api">`null`</span> ở lần render đầu tiên. Lý do là phần tử chưa tồn tại cho đến sau lần render đầu tiên!

<div class="composition-api">

Nếu bạn muốn theo dõi các thay đổi của một template ref, hãy đảm bảo xử lý trường hợp ref có giá trị `null`:

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // not mounted yet, or the element was unmounted (e.g. by v-if)
  }
})
```

Xem thêm: [Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Ref trên Component {#ref-on-component}

> Phần này yêu cầu bạn đã có kiến thức về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua và quay lại sau.

`ref` cũng có thể được dùng trên một component con. Trong trường hợp này, tham chiếu sẽ là instance của component đó:

<div class="composition-api">

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // childRef.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

<details>
<summary>Usage before 3.5</summary>

```vue
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value will hold an instance of <Child />
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
    // this.$refs.child will hold an instance of <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>

<span class="composition-api">Nếu component con sử dụng Options API hoặc không dùng `<script setup>`, instance</span><span class="options-api">Instance</span> được tham chiếu sẽ giống hệt `this` của component con, nghĩa là component cha sẽ có toàn quyền truy cập vào mọi thuộc tính và phương thức của component con. Điều này dễ dẫn đến sự phụ thuộc chặt chẽ về chi tiết triển khai giữa cha và con, vì vậy nên dùng template ref cho component chỉ khi thực sự cần thiết — trong hầu hết trường hợp, hãy ưu tiên tương tác cha/con thông qua props và emit trước.

<div class="composition-api">

Ngoại lệ ở đây là các component dùng `<script setup>` — chúng **mặc định là private**: một component cha tham chiếu đến component con dùng `<script setup>` sẽ không thể truy cập bất kỳ thứ gì trừ khi component con chủ động expose một giao diện công khai bằng macro `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Compiler macros, such as defineExpose, don't need to be imported
defineExpose({
  a,
  b
})
</script>
```

Khi component cha lấy được instance của component này qua template ref, instance nhận được sẽ có dạng `{ a: number, b: number }` (các ref được tự động mở bọc ref giống như trên các instance thông thường).

Lưu ý rằng `defineExpose` phải được gọi trước bất kỳ thao tác `await` nào. Nếu không, các thuộc tính và phương thức được expose sau thao tác `await` sẽ không thể truy cập được.

Xem thêm: [Typing Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Tùy chọn `expose` có thể được dùng để giới hạn quyền truy cập vào instance của component con:

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

Trong ví dụ trên, một component cha tham chiếu đến component này qua template ref sẽ chỉ có thể truy cập `publicData` và `publicMethod`.

</div>

## Refs bên trong `v-for` {#refs-inside-v-for}

> Yêu cầu v3.5 trở lên

<div class="composition-api">

Khi `ref` được dùng bên trong `v-for`, ref tương ứng cần chứa giá trị là một mảng (Array), và mảng này sẽ được điền đầy đủ các phần tử sau khi mount:

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

[Try it in the Playground](https://play.vuejs.org/#eNp9UsluwjAQ/ZWRLwQpDepyQoDUIg6t1EWUW91DFAZq6tiWF4oU5d87dtgqVRyyzLw3b+aN3bB7Y4ptQDZkI1dZYTw49MFMuBK10dZDAxZXOQSHC6yNLD3OY6zVsw7K4xJaWFldQ49UelxxVWnlPEhBr3GszT6uc7jJ4fazf4KFx5p0HFH+Kme9CLle4h6bZFkfxhNouAIoJVqfHQSKbSkDFnVpMhEpovC481NNVcr3SaWlZzTovJErCqgydaMIYBRk+tKfFLC9Wmk75iyqg1DJBWfRxT7pONvTAZom2YC23QsMpOg0B0l0NDh2YjnzjpyvxLrYOK1o3ckLZ5WujSBHr8YL2gxnw85lxEop9c9TynkbMD/kqy+svv/Jb9wu5jh7s+jQbpGzI+ZLu0byEuHZ+wvt6Ays9TJIYl8A5+i0DHHGjvYQ1JLGPuOlaR/TpRFqvXCzHR2BO5iKg0Zmm/ic0W2ZXrB+Gve2uEt1dJKs/QXbwePE)

<details>
<summary>Usage before 3.5</summary>

In versions before 3.5 where `useTemplateRef()` was not introduced, we need to declare a ref with a name that matches the template ref attribute's value. The ref should also contain an array value:

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

Khi `ref` được dùng bên trong `v-for`, giá trị ref kết quả sẽ là một mảng chứa các phần tử tương ứng:

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

[Try it in the Playground](https://play.vuejs.org/#eNpFjk0KwjAQha/yCC4Uaou6kyp4DuOi2KkGYhKSiQildzdNa4WQmTc/37xeXJwr35HEUdTh7pXjszT0cdYzWuqaqBm9NEDbcLPeTDngiaM3PwVoFfiI667AvsDhNpWHMQzF+L9sNEztH3C3JlhNpbaPNT9VKFeeulAqplfY5D1p0qurxVQSqel0w5QUUEedY8q0wnvbWX+SYgRAmWxIiuSzm4tBinkc6HvkuSE7TIBKq4lZZWhdLZfE8AWp4l3T)

</div>

Cần lưu ý rằng mảng ref **không** đảm bảo thứ tự giống với mảng nguồn.

## Function Refs {#function-refs}

Thay vì một chuỗi key, thuộc tính `ref` cũng có thể được gắn với một hàm, hàm này sẽ được gọi mỗi khi component cập nhật và cho bạn toàn quyền kiểm soát nơi lưu trữ tham chiếu phần tử. Hàm nhận tham chiếu phần tử làm đối số đầu tiên:

```vue-html
<input :ref="(el) => { /* assign el to a property or ref */ }">
```

Lưu ý chúng ta đang dùng binding động `:ref` để có thể truyền vào một hàm thay vì một chuỗi tên ref. Khi phần tử được unmount, đối số sẽ là `null`. Tất nhiên, bạn có thể dùng một phương thức thay vì hàm inline.
