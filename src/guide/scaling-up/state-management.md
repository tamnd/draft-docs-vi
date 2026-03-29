# Quản Lý State {#state-management}

## Quản Lý State Là Gì? {#what-is-state-management}

Về mặt kỹ thuật, mỗi instance component Vue vốn đã tự "quản lý" state phản ứng của riêng nó. Hãy lấy một component bộ đếm đơn giản làm ví dụ:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

// state
const count = ref(0)

// action
function increment() {
  count.value++
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  // state
  data() {
    return {
      count: 0
    }
  },
  // action
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>

Đây là một đơn vị tự chứa với các phần sau:

- **State**, nguồn dữ liệu chuẩn điều khiển ứng dụng.
- **View**, phép ánh xạ khai báo của **state**.
- **Action**, những cách mà state có thể thay đổi để phản ứng với đầu vào của người dùng từ **view**.

Đây là cách biểu diễn đơn giản của khái niệm "luồng dữ liệu một chiều":

<p style="text-align: center">
  <img alt="state flow diagram" src="./images/state-flow.png" width="252px" style="margin: 40px auto">
</p>

Tuy nhiên, sự đơn giản này bắt đầu không còn giữ được khi ta có **nhiều component dùng chung một state**:

1. Nhiều view có thể cùng phụ thuộc vào một mẩu state.
2. Action từ các view khác nhau có thể cần thay đổi cùng một mẩu state.

Với trường hợp thứ nhất, một cách xử lý là "nâng" state dùng chung lên một component tổ tiên chung, rồi truyền nó xuống dưới qua props. Nhưng cách này nhanh chóng trở nên rườm rà trong cây component nhiều tầng, dẫn đến vấn đề quen thuộc là [prop drilling](/guide/components/provide-inject#prop-drilling).

Với trường hợp thứ hai, ta thường phải dùng đến những cách như truy cập trực tiếp instance cha / con qua template ref, hoặc cố thay đổi và đồng bộ nhiều bản sao của state thông qua sự kiện emit. Cả hai mẫu này đều mong manh và rất nhanh dẫn đến mã khó bảo trì.

Một cách đơn giản và trực tiếp hơn là tách state dùng chung ra khỏi component, rồi quản lý nó trong một singleton toàn cục. Khi đó, toàn bộ cây component của ta trở thành một "view" lớn, và bất kỳ component nào cũng có thể truy cập state hoặc kích hoạt action, bất kể nó nằm ở đâu trong cây.

## Quản Lý State Đơn Giản Với Reactivity API {#simple-state-management-with-reactivity-api}

<div class="options-api">

Trong Options API, dữ liệu phản ứng được khai báo bằng option `data()`. Bên trong, object trả về từ `data()` sẽ được biến thành reactive thông qua hàm [`reactive()`](/api/reactivity-core#reactive), vốn cũng được cung cấp như một API công khai.

</div>

Nếu bạn có một mẩu state cần được chia sẻ giữa nhiều instance, bạn có thể dùng [`reactive()`](/api/reactivity-core#reactive) để tạo object reactive, rồi import nó vào nhiều component:

```js [store.js]
import { reactive } from 'vue'

export const store = reactive({
  count: 0
})
```

<div class="composition-api">

```vue [ComponentA.vue]
<script setup>
import { store } from './store.js'
</script>

<template>Từ A: {{ store.count }}</template>
```

```vue [ComponentB.vue]
<script setup>
import { store } from './store.js'
</script>

<template>Từ B: {{ store.count }}</template>
```

</div>
<div class="options-api">

```vue [ComponentA.vue]
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>Từ A: {{ store.count }}</template>
```

```vue [ComponentB.vue]
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>Từ B: {{ store.count }}</template>
```

</div>

Giờ đây, bất cứ khi nào object `store` bị thay đổi, cả `<ComponentA>` lẫn `<ComponentB>` đều sẽ tự động cập nhật view. Ta đã có một nguồn dữ liệu chuẩn duy nhất.

Tuy nhiên, điều đó cũng đồng nghĩa với việc bất kỳ component nào import `store` cũng có thể sửa nó theo bất cứ cách nào:

```vue-html{2}
<template>
  <button @click="store.count++">
    Từ B: {{ store.count }}
  </button>
</template>
```

Dù điều này hoạt động trong trường hợp đơn giản, một global state có thể bị thay đổi tùy tiện bởi bất kỳ component nào sẽ rất khó bảo trì về lâu dài. Để bảo đảm logic thay đổi state được tập trung giống như chính state, bạn nên định nghĩa các method ngay trên store với tên thể hiện rõ ý định của action:

```js{5-7} [store.js]
import { reactive } from 'vue'

export const store = reactive({
  count: 0,
  increment() {
    this.count++
  }
})
```

```vue-html{2}
<template>
  <button @click="store.increment()">
    Từ B: {{ store.count }}
  </button>
</template>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNrNkk1uwyAQha8yYpNEiUzXllPVrtRTeJNSqtLGgGBsVbK4ewdwnT9FWWSTFczwmPc+xMhqa4uhl6xklRdOWQQvsbfPrVadNQ7h1dCqpcYaPp3pYFHwQyteXVxKm0tpM0krnm3IgAqUnd3vUFIFUB1Z8bNOkzoVny+wDTuNcZ1gBI/GSQhzqlQX3/5Gng81pA1t33tEo+FF7JX42bYsT1BaONlRguWqZZMU4C261CWMk3EhTK8RQphm8Twse/BscoUsvdqDkTX3kP3nI6aZwcmdQDUcMPJPabX8TQphtCf0RLqd1csxuqQAJTxtYnEUGtIpAH4pn1Ou17FDScOKhT+QNAVM)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNrdU8FqhDAU/JVHLruyi+lZ3FIt9Cu82JilaTWR5CkF8d8bE5O1u1so9FYQzAyTvJnRTKTo+3QcOMlIbpgWPT5WUnS90gjPyr4ll1jAWasOdim9UMum3a20vJWWqxSgkvzTyRt+rocWYVpYFoQm8wRsJh+viHLBcyXtk9No2ALkXd/WyC0CyDfW6RVTOiancQM5ku+x7nUxgUGlOcwxn8Ppu7HJ7udqaqz3SYikOQ5aBgT+OA9slt9kasToFnb5OiAqCU+sFezjVBHvRUimeWdT7JOKrFKAl8VvYatdI6RMDRJhdlPtWdQf5mdQP+SHdtyX/IftlH9pJyS1vcQ2NK8ZivFSiL8BsQmmpMG1s1NU79frYA1k8OD+/I3pUA6+CeNdHg6hmoTMX9pPSnk=)

</div>

:::tip
Lưu ý handler của `click` dùng `store.increment()` kèm dấu ngoặc. Điều này là cần thiết để gọi method với đúng ngữ cảnh `this`, vì đây không phải method của component.
:::

Dù ở đây ta dùng một object reactive duy nhất làm store, bạn cũng có thể chia sẻ state phản ứng được tạo bằng các [Reactivity API](/api/reactivity-core) khác như `ref()` hoặc `computed()`, hoặc thậm chí trả về global state từ một [Composable](/guide/reusability/composables):

```js
import { ref } from 'vue'

// global state, được tạo ở phạm vi module
const globalCount = ref(1)

export function useCount() {
  // local state, được tạo cho từng component
  const localCount = ref(1)

  return {
    globalCount,
    localCount
  }
}
```

Việc hệ phản ứng của Vue được tách khỏi mô hình component khiến nó cực kỳ linh hoạt.

## Lưu Ý Với SSR {#ssr-considerations}

Nếu bạn đang xây một ứng dụng dùng [Server-Side Rendering (SSR)](./ssr), pattern bên trên có thể gây vấn đề vì store là một singleton dùng chung giữa nhiều request. Nội dung này được giải thích [chi tiết hơn](./ssr#cross-request-state-pollution) trong hướng dẫn SSR.

## Pinia {#pinia}

Dù giải pháp quản lý state tự viết tay ở trên đủ dùng trong các tình huống đơn giản, ở ứng dụng production quy mô lớn còn có nhiều điều khác cần cân nhắc:

- Quy ước chặt chẽ hơn cho việc cộng tác trong nhóm
- Tích hợp với Vue DevTools, bao gồm timeline, kiểm tra ngay trong component, và time-travel debugging
- Hot Module Replacement
- Hỗ trợ Server-Side Rendering

[Pinia](https://pinia.vuejs.org) là thư viện quản lý state hiện thực đầy đủ những điều trên. Nó được đội ngũ Vue core duy trì và hoạt động với cả Vue 2 lẫn Vue 3.

Người dùng lâu năm có thể quen với [Vuex](https://vuex.vuejs.org/), thư viện quản lý state chính thức trước đây của Vue. Vì Pinia hiện đảm nhiệm vai trò đó trong hệ sinh thái, Vuex giờ ở chế độ bảo trì. Nó vẫn hoạt động, nhưng sẽ không còn nhận thêm tính năng mới. Với ứng dụng mới, bạn nên dùng Pinia.

Pinia ban đầu được tạo ra như một bước khám phá xem thế hệ tiếp theo của Vuex nên trông như thế nào, kết hợp nhiều ý tưởng từ các cuộc thảo luận của đội ngũ core về Vuex 5. Cuối cùng, chúng tôi nhận ra Pinia đã hiện thực được phần lớn những gì mình muốn ở Vuex 5, nên quyết định biến nó thành khuyến nghị chính thức mới.

So với Vuex, Pinia cung cấp API đơn giản hơn với ít nghi thức hơn, hỗ trợ API theo phong cách Composition API, và quan trọng nhất là có type inference rất tốt khi dùng với TypeScript.
