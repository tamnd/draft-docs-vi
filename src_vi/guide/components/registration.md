# Đăng Ký Component {#component-registration}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-global-vs-local-vue-components" title="Bài học miễn phí về đăng ký component trong Vue.js"/>

Một component Vue cần được "đăng ký" để Vue biết phải tìm phần cài đặt của nó ở đâu khi gặp component đó trong template. Có hai cách đăng ký component: global và local.

## Đăng Ký Global {#global-registration}

Ta có thể làm cho component khả dụng trên toàn bộ [ứng dụng Vue](/guide/essentials/application) hiện tại bằng phương thức `.component()`:

```js
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // tên đã đăng ký
  'MyComponent',
  // phần cài đặt
  {
    /* ... */
  }
)
```

Nếu dùng SFC, bạn sẽ đăng ký các file `.vue` đã import:

```js
import MyComponent from './App.vue'

app.component('MyComponent', MyComponent)
```

Phương thức `.component()` có thể được gọi theo kiểu chain:

```js
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

Component được đăng ký global có thể được dùng trong template của mọi component bên trong ứng dụng này:

```vue-html
<!-- sẽ hoạt động trong bất kỳ component nào bên trong app -->
<ComponentA/>
<ComponentB/>
<ComponentC/>
```

Điều này cũng áp dụng cho mọi component con, nghĩa là cả ba component trên cũng đều khả dụng khi dùng _bên trong nhau_.

## Đăng Ký Local {#local-registration}

Dù tiện lợi, đăng ký global vẫn có một vài nhược điểm:

1. Đăng ký global khiến hệ thống build không thể loại bỏ component không dùng đến (hay còn gọi là "tree-shaking"). Nếu bạn đăng ký global một component nhưng cuối cùng không dùng nó ở đâu trong app, nó vẫn sẽ bị đưa vào bundle cuối cùng.

2. Đăng ký global khiến quan hệ phụ thuộc kém rõ ràng hơn trong các ứng dụng lớn. Việc lần ra phần cài đặt của component con từ component cha đang sử dụng nó sẽ khó hơn. Điều này có thể ảnh hưởng đến khả năng bảo trì về lâu dài, tương tự như khi lạm dụng biến global.

Đăng ký local giới hạn phạm vi sử dụng của component đã đăng ký trong chính component hiện tại. Cách này khiến quan hệ phụ thuộc rõ ràng hơn và thân thiện với tree-shaking hơn.

<div class="composition-api">

Khi dùng SFC với `<script setup>`, component đã import có thể được dùng cục bộ mà không cần đăng ký:

```vue
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

Nếu không dùng `<script setup>`, bạn cần dùng option `components`:

```js
import ComponentA from './ComponentA.js'

export default {
  components: {
    ComponentA
  },
  setup() {
    // ...
  }
}
```

</div>
<div class="options-api">

Đăng ký local được thực hiện bằng option `components`:

```vue
<script>
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
}
</script>

<template>
  <ComponentA />
</template>
```

</div>

Với mỗi property trong object `components`, key sẽ là tên đăng ký của component, còn value là phần cài đặt của component đó. Ví dụ trên đang dùng cú pháp rút gọn property của ES2015 và tương đương với:

```js
export default {
  components: {
    ComponentA: ComponentA
  }
  // ...
}
```

Lưu ý rằng **component được đăng ký local sẽ _không_ tự động khả dụng trong component hậu duệ**. Trong trường hợp này, `ComponentA` chỉ khả dụng trong component hiện tại, không khả dụng trong các component con hoặc hậu duệ của nó.

## Kiểu Chữ Trong Tên Component {#component-name-casing}

Trong suốt tài liệu này, chúng ta dùng tên PascalCase khi đăng ký component. Lý do là vì:

1. Tên PascalCase là định danh JavaScript hợp lệ. Điều này giúp việc import và đăng ký component trong JavaScript dễ dàng hơn. Nó cũng giúp IDE tự động hoàn thành tốt hơn.

2. `<PascalCase />` làm cho việc nhận ra đây là component Vue thay vì phần tử HTML gốc trong template trở nên rõ ràng hơn. Nó cũng giúp phân biệt component Vue với custom element (web component).

Đây là style được khuyến nghị khi làm việc với SFC hoặc string template. Tuy vậy, như đã đề cập trong [Lưu ý khi phân tích template viết trực tiếp trong DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats), thẻ PascalCase không dùng được trong template viết trực tiếp trong DOM.

May mắn là Vue hỗ trợ ánh xạ thẻ kebab-case sang component được đăng ký bằng PascalCase. Điều đó có nghĩa là một component được đăng ký với tên `MyComponent` có thể được tham chiếu trong template Vue (hoặc bên trong phần tử HTML do Vue render) bằng cả `<MyComponent>` lẫn `<my-component>`. Nhờ đó, ta có thể dùng cùng một đoạn mã đăng ký component trong JavaScript bất kể template đến từ nguồn nào.
