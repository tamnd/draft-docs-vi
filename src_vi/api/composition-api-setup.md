# Composition API: setup() {#composition-api-setup}

## Sử dụng Cơ bản {#basic-usage}

Hook `setup()` đóng vai trò là điểm vào cho việc sử dụng Composition API trong component trong các trường hợp sau:

1. Dùng Composition API mà không có bước build;
2. Tích hợp với code dựa trên Composition API trong một component Options API.

:::info Lưu ý
Nếu bạn đang dùng Composition API với Single-File Component, [`<script setup>`](/api/sfc-script-setup) được khuyến nghị mạnh mẽ để có cú pháp ngắn gọn và tiện lợi hơn.
:::

Ta có thể khai báo state phản ứng bằng [Reactivity API](./reactivity-core) và expose chúng ra template bằng cách trả về một object từ `setup()`. Các thuộc tính trên object được trả về cũng sẽ có sẵn trên instance component (nếu có các option khác được dùng):

```vue
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // expose ra template và các hook Options API khác
    return {
      count
    }
  },

  mounted() {
    console.log(this.count) // 0
  }
}
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

[ref](/api/reactivity-core#ref) được trả về từ `setup` sẽ được [tự động unwrap nông](/guide/essentials/reactivity-fundamentals#deep-reactivity) khi truy cập trong template, vì vậy bạn không cần dùng `.value` khi truy cập chúng. Chúng cũng được unwrap theo cách tương tự khi truy cập qua `this`.

`setup()` bản thân nó không có quyền truy cập vào instance component - `this` sẽ có giá trị `undefined` bên trong `setup()`. Bạn có thể truy cập các giá trị được expose qua Composition API từ Options API, nhưng không phải ngược lại.

`setup()` nên trả về một object _đồng bộ_. Trường hợp duy nhất có thể dùng `async setup()` là khi component là con của một component [Suspense](../guide/built-ins/suspense).

## Truy cập Props {#accessing-props}

Đối số đầu tiên trong hàm `setup` là đối số `props`. Đúng như bạn mong đợi trong một component chuẩn, `props` bên trong hàm `setup` có tính phản ứng và sẽ được cập nhật khi có props mới được truyền vào.

```js
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

Lưu ý rằng nếu bạn destructure object `props`, các biến destructure sẽ mất tính phản ứng. Vì vậy khuyến nghị luôn truy cập props dưới dạng `props.xxx`.

Nếu bạn thực sự cần destructure props, hoặc cần truyền một prop vào hàm bên ngoài trong khi vẫn giữ tính phản ứng, bạn có thể làm điều đó với các API tiện ích [toRefs()](./reactivity-utilities#torefs) và [toRef()](/api/reactivity-utilities#toref):

```js
import { toRefs, toRef } from 'vue'

export default {
  setup(props) {
    // chuyển `props` thành object gồm các ref, rồi destructure
    const { title } = toRefs(props)
    // `title` là ref theo dõi `props.title`
    console.log(title.value)

    // HOẶC, chuyển một thuộc tính đơn lẻ trên `props` thành ref
    const title = toRef(props, 'title')
  }
}
```

## Setup Context {#setup-context}

Đối số thứ hai truyền vào hàm `setup` là một object **Setup Context**. Context object expose các giá trị khác có thể hữu ích bên trong `setup`:

```js
export default {
  setup(props, context) {
    // Thuộc tính (object không có tính phản ứng, tương đương $attrs)
    console.log(context.attrs)

    // Slot (object không có tính phản ứng, tương đương $slots)
    console.log(context.slots)

    // Emit sự kiện (Hàm, tương đương $emit)
    console.log(context.emit)

    // Expose các thuộc tính công khai (Hàm)
    console.log(context.expose)
  }
}
```

Context object không có tính phản ứng và có thể destructure an toàn:

```js
export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

`attrs` và `slots` là các object có trạng thái, luôn được cập nhật khi chính component đó được cập nhật. Điều này có nghĩa bạn nên tránh destructure chúng và luôn tham chiếu các thuộc tính dưới dạng `attrs.x` hoặc `slots.x`. Cũng lưu ý rằng, không giống `props`, các thuộc tính của `attrs` và `slots` **không** có tính phản ứng. Nếu bạn có ý định áp dụng side effect dựa trên thay đổi của `attrs` hoặc `slots`, bạn nên làm điều đó bên trong hook vòng đời `onBeforeUpdate`.

### Expose Thuộc tính Công khai {#exposing-public-properties}

`expose` là một hàm có thể dùng để giới hạn rõ ràng các thuộc tính được expose khi instance component được component cha truy cập qua [template ref](/guide/essentials/template-refs#ref-on-component):

```js{5,10}
export default {
  setup(props, { expose }) {
    // làm cho instance "đóng" -
    // tức là không expose bất cứ thứ gì ra cha
    expose()

    const publicCount = ref(0)
    const privateCount = ref(0)
    // chọn lọc expose state cục bộ
    expose({ count: publicCount })
  }
}
```

## Dùng với Render Function {#usage-with-render-functions}

`setup` cũng có thể trả về một [render function](/guide/extras/render-function) có thể trực tiếp sử dụng state phản ứng được khai báo trong cùng phạm vi:

```js{6}
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('div', count.value)
  }
}
```

Trả về render function ngăn ta trả về bất cứ thứ gì khác. Về mặt nội bộ điều này không có vấn đề, nhưng có thể gây vấn đề nếu ta muốn expose các phương thức của component này ra component cha qua template ref.

Ta có thể giải quyết vấn đề này bằng cách gọi [`expose()`](#exposing-public-properties):

```js{8-10}
import { h, ref } from 'vue'

export default {
  setup(props, { expose }) {
    const count = ref(0)
    const increment = () => ++count.value

    expose({
      increment
    })

    return () => h('div', count.value)
  }
}
```

Phương thức `increment` sau đó sẽ có sẵn trong component cha qua template ref.
