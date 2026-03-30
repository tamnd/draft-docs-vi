# Element Đặc biệt Dựng sẵn {#built-in-special-elements}

:::info Không phải Component
`<component>`, `<slot>` và `<template>` là các tính năng giống component và là một phần của cú pháp template. Chúng không phải component thực sự và được biên dịch bỏ đi trong quá trình biên dịch template. Vì vậy, theo quy ước chúng được viết bằng chữ thường trong template.
:::

## `<component>` {#component}

Một "meta component" để render các component hoặc element động.

- **Props**

  ```ts
  interface DynamicComponentProps {
    is: string | Component
  }
  ```

- **Chi tiết**

  Component thực sự được render được xác định bởi prop `is`.

  - Khi `is` là string, nó có thể là tên thẻ HTML hoặc tên đã đăng ký của một component.

  - Ngoài ra, `is` cũng có thể được ràng buộc trực tiếp với định nghĩa của một component.

- **Ví dụ**

  Render component theo tên đã đăng ký (Options API):

  ```vue
  <script>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: { Foo, Bar },
    data() {
      return {
        view: 'Foo'
      }
    }
  }
  </script>

  <template>
    <component :is="view" />
  </template>
  ```

  Render component theo định nghĩa (Composition API với `<script setup>`):

  ```vue
  <script setup>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'
  </script>

  <template>
    <component :is="Math.random() > 0.5 ? Foo : Bar" />
  </template>
  ```

  Render các element HTML:

  ```vue-html
  <component :is="href ? 'a' : 'span'"></component>
  ```

  Tất cả [component dựng sẵn](./built-in-components) đều có thể truyền vào `is`, nhưng bạn phải đăng ký chúng nếu muốn truyền theo tên. Ví dụ:

  ```vue
  <script>
  import { Transition, TransitionGroup } from 'vue'

  export default {
    components: {
      Transition,
      TransitionGroup
    }
  }
  </script>

  <template>
    <component :is="isGroup ? 'TransitionGroup' : 'Transition'">
      ...
    </component>
  </template>
  ```

  Không cần đăng ký nếu bạn truyền chính component đó vào `is` thay vì tên của nó, ví dụ trong `<script setup>`.

  Nếu `v-model` được dùng trên thẻ `<component>`, trình biên dịch template sẽ mở rộng nó thành prop `modelValue` và event listener `update:modelValue`, giống như với bất kỳ component nào khác. Tuy nhiên, điều này sẽ không tương thích với các element HTML native, như `<input>` hoặc `<select>`. Kết quả là, dùng `v-model` với một element native được tạo động sẽ không hoạt động:

  ```vue
  <script setup>
  import { ref } from 'vue'

  const tag = ref('input')
  const username = ref('')
  </script>

  <template>
    <!-- Điều này sẽ không hoạt động vì 'input' là element HTML native -->
    <component :is="tag" v-model="username" />
  </template>
  ```

  Trong thực tế, trường hợp đặc biệt này không phổ biến vì các trường form native thường được bọc trong component trong ứng dụng thực tế. Nếu bạn thực sự cần dùng element native trực tiếp thì bạn có thể tách `v-model` thành thuộc tính và sự kiện thủ công.

- **Xem thêm** [Component Động](/guide/essentials/component-basics#dynamic-components)

## `<slot>` {#slot}

Biểu thị vị trí hiển thị nội dung slot trong template.

- **Props**

  ```ts
  interface SlotProps {
    /**
     * Bất kỳ prop nào truyền vào <slot> sẽ được chuyển thành đối số
     * cho scoped slot
     */
    [key: string]: any
    /**
     * Dành riêng để chỉ định tên slot.
     */
    name?: string
  }
  ```

- **Chi tiết**

  Element `<slot>` có thể dùng thuộc tính `name` để chỉ định tên slot. Khi không có `name`, nó sẽ render default slot. Các thuộc tính bổ sung truyền vào slot element sẽ được chuyển thành slot props cho scoped slot được định nghĩa trong cha.

  Bản thân element sẽ được thay thế bởi nội dung slot khớp với nó.

  Các element `<slot>` trong Vue template được biên dịch thành JavaScript, vì vậy không nên nhầm lẫn chúng với [element `<slot>` native](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot).

- **Xem thêm** [Component - Slot](/guide/components/slots)

## `<template>` {#template}

Thẻ `<template>` được dùng làm placeholder khi ta muốn dùng directive dựng sẵn mà không render một element trong DOM.

- **Chi tiết**

  Xử lý đặc biệt cho `<template>` chỉ được kích hoạt nếu nó được dùng với một trong các directive sau:

  - `v-if`, `v-else-if`, hoặc `v-else`
  - `v-for`
  - `v-slot`

  Nếu không có directive nào trong số đó, nó sẽ được render như một [element `<template>` native](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) thay thế.

  Một `<template>` có `v-for` cũng có thể có [thuộc tính `key`](/api/built-in-special-attributes#key). Tất cả các thuộc tính và directive khác sẽ bị bỏ qua vì chúng không có ý nghĩa nếu không có element tương ứng.

  Single-file component dùng [thẻ `<template>` cấp cao nhất](/api/sfc-spec#language-blocks) để bọc toàn bộ template. Cách dùng đó tách biệt với cách dùng `<template>` được mô tả ở trên. Thẻ cấp cao nhất đó không phải là một phần của template và không hỗ trợ cú pháp template, chẳng hạn như directive.

- **Xem thêm**
  - [Hướng dẫn - `v-if` trên `<template>`](/guide/essentials/conditional#v-if-on-template)
  - [Hướng dẫn - `v-for` trên `<template>`](/guide/essentials/list#v-for-on-template)
  - [Hướng dẫn - Slot có tên](/guide/components/slots#named-slots)
