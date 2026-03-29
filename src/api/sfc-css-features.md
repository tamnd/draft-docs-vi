# Tính năng CSS trong SFC {#sfc-css-features}

## Scoped CSS {#scoped-css}

Khi tag `<style>` có thuộc tính `scoped`, CSS của nó chỉ áp dụng cho các element của component hiện tại. Điều này tương tự như đóng gói style trong Shadow DOM. Nó đi kèm với một số lưu ý, nhưng không yêu cầu bất kỳ polyfill nào. Điều này được thực hiện bằng cách dùng PostCSS để chuyển đổi như sau:

```vue
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```

Thành như sau:

```vue
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>
```

### Element Gốc của Component Con {#child-component-root-elements}

Với `scoped`, các style của component cha sẽ không rò rỉ vào các component con. Tuy nhiên, node gốc của component con sẽ bị ảnh hưởng bởi cả scoped CSS của cha lẫn scoped CSS của con. Đây là thiết kế có chủ ý để cha có thể style element gốc của con cho mục đích bố cục.

### Deep Selector {#deep-selectors}

Nếu bạn muốn một selector trong style `scoped` là "deep", tức là ảnh hưởng đến component con, bạn có thể dùng pseudo-class `:deep()`:

```vue
<style scoped>
.a :deep(.b) {
  /* ... */
}
</style>
```

Phần trên sẽ được biên dịch thành:

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

:::tip
Nội dung DOM được tạo bằng `v-html` không bị ảnh hưởng bởi scoped style, nhưng bạn vẫn có thể style chúng bằng deep selector.
:::

### Slotted Selector {#slotted-selectors}

Mặc định, scoped style không ảnh hưởng đến nội dung được render bởi `<slot/>`, vì chúng được coi là thuộc về component cha đang truyền chúng vào. Để nhắm mục tiêu rõ ràng nội dung slot, dùng pseudo-class `:slotted`:

```vue
<style scoped>
:slotted(div) {
  color: red;
}
</style>
```

### Global Selector {#global-selectors}

Nếu bạn chỉ muốn một rule áp dụng toàn cục, bạn có thể dùng pseudo-class `:global` thay vì tạo thêm `<style>` khác (xem bên dưới):

```vue
<style scoped>
:global(.red) {
  color: red;
}
</style>
```

### Pha trộn Style Cục bộ và Toàn cục {#mixing-local-and-global-styles}

Bạn cũng có thể bao gồm cả style scoped và non-scoped trong cùng một component:

```vue
<style>
/* style toàn cục */
</style>

<style scoped>
/* style cục bộ */
</style>
```

### Lưu ý về Scoped Style {#scoped-style-tips}

- **Scoped style không loại bỏ nhu cầu dùng class**. Do cách trình duyệt render các CSS selector khác nhau, `p { color: red }` sẽ chậm hơn nhiều lần khi scoped (tức là khi kết hợp với attribute selector). Nếu bạn dùng class hoặc id thay thế, ví dụ `.example { color: red }`, thì bạn thực tế loại bỏ được vấn đề hiệu suất đó.

- **Cẩn thận với descendant selector trong recursive component!** Đối với CSS rule với selector `.a .b`, nếu element khớp với `.a` chứa một recursive child component, thì tất cả `.b` trong child component đó sẽ khớp với rule đó.

## CSS Module {#css-modules}

Tag `<style module>` được biên dịch như [CSS Module](https://github.com/css-modules/css-modules) và expose các CSS class kết quả cho component như một object dưới key `$style`:

```vue
<template>
  <p :class="$style.red">Phần này nên màu đỏ</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

Các class kết quả được hash để tránh xung đột, đạt được cùng hiệu ứng scoping CSS chỉ cho component hiện tại.

Tham khảo [đặc tả CSS Module](https://github.com/css-modules/css-modules) để biết thêm chi tiết như [global exception](https://github.com/css-modules/css-modules/blob/master/docs/composition.md#exceptions) và [composition](https://github.com/css-modules/css-modules/blob/master/docs/composition.md#composition).

### Tên Inject Tùy chỉnh {#custom-inject-name}

Bạn có thể tùy chỉnh property key của object class được inject bằng cách cho thuộc tính `module` một giá trị:

```vue
<template>
  <p :class="classes.red">red</p>
</template>

<style module="classes">
.red {
  color: red;
}
</style>
```

### Dùng với Composition API {#usage-with-composition-api}

Các class được inject có thể truy cập trong `setup()` và `<script setup>` qua API `useCssModule`. Đối với các khối `<style module>` với tên inject tùy chỉnh, `useCssModule` chấp nhận giá trị thuộc tính `module` khớp làm đối số đầu tiên:

```js
import { useCssModule } from 'vue'

// bên trong scope setup()...
// mặc định, trả về các class cho <style module>
useCssModule()

// có tên, trả về các class cho <style module="classes">
useCssModule('classes')
```

- **Ví dụ**

```vue
<script setup lang="ts">
import { useCssModule } from 'vue'

const classes = useCssModule()
</script>

<template>
  <p :class="classes.red">red</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

## `v-bind()` trong CSS {#v-bind-in-css}

Tag `<style>` trong SFC hỗ trợ liên kết các giá trị CSS với dynamic component state bằng cách dùng hàm CSS `v-bind`:

```vue
<template>
  <div class="text">hello</div>
</template>

<script>
export default {
  data() {
    return {
      color: 'red'
    }
  }
}
</script>

<style>
.text {
  color: v-bind(color);
}
</style>
```

Cú pháp này hoạt động với [`<script setup>`](./sfc-script-setup), và hỗ trợ biểu thức JavaScript (phải được bọc trong dấu ngoặc kép):

```vue
<script setup>
import { ref } from 'vue'
const theme = ref({
    color: 'red',
})
</script>

<template>
  <p>hello</p>
</template>

<style scoped>
p {
  color: v-bind('theme.color');
}
</style>
```

Giá trị thực sự sẽ được biên dịch thành một CSS custom property được hash, vì vậy CSS vẫn là static. Custom property sẽ được áp dụng vào element gốc của component qua inline style và được cập nhật reactive nếu giá trị nguồn thay đổi.
