# Quy tắc Ưu tiên C: Khuyến nghị {#priority-c-rules-recommended}

::: warning Lưu ý
Hướng dẫn phong cách Vue.js này đã lỗi thời và cần được xem xét lại. Nếu bạn có câu hỏi hoặc gợi ý, vui lòng [mở issue](https://github.com/vuejs/docs/issues/new).
:::

Khi có nhiều lựa chọn tốt như nhau, có thể chọn tùy ý để đảm bảo nhất quán. Trong các quy tắc này, chúng tôi mô tả từng lựa chọn chấp nhận được và đề xuất mặc định. Điều đó có nghĩa là bạn hoàn toàn có thể chọn khác trong codebase của mình, miễn là nhất quán và có lý do chính đáng. Hãy thực sự có lý do! Khi thích nghi với tiêu chuẩn cộng đồng, bạn sẽ:

1. Rèn não để phân tích phần lớn code cộng đồng dễ dàng hơn
2. Có thể sao chép dán hầu hết ví dụ code của cộng đồng mà không cần sửa đổi
3. Thường thấy nhân viên mới đã quen với phong cách code bạn ưa thích, ít nhất liên quan đến Vue

## Thứ tự option của component/instance {#component-instance-options-order}

**Các option của component/instance nên được sắp xếp theo thứ tự nhất quán.**

Đây là thứ tự mặc định chúng tôi khuyến nghị cho các option component. Chúng được chia thành các danh mục để bạn biết nơi thêm các property mới từ plugin.

1. **Global Awareness** (yêu cầu kiến thức ngoài component)

   - `name`

2. **Template Compiler Options** (thay đổi cách template được biên dịch)

   - `compilerOptions`

3. **Template Dependencies** (asset dùng trong template)

   - `components`
   - `directives`

4. **Composition** (hợp nhất property vào options)

   - `extends`
   - `mixins`
   - `provide`/`inject`

5. **Interface** (giao diện của component)

   - `inheritAttrs`
   - `props`
   - `emits`

6. **Composition API** (điểm vào để dùng Composition API)

   - `setup`

7. **Local State** (các property reactive cục bộ)

   - `data`
   - `computed`

8. **Events** (callback được kích hoạt bởi các sự kiện reactive)

   - `watch`
   - Lifecycle Events (theo thứ tự được gọi)
     - `beforeCreate`
     - `created`
     - `beforeMount`
     - `mounted`
     - `beforeUpdate`
     - `updated`
     - `activated`
     - `deactivated`
     - `beforeUnmount`
     - `unmounted`
     - `errorCaptured`
     - `renderTracked`
     - `renderTriggered`

9. **Non-Reactive Properties** (property instance độc lập với hệ thống reactivity)

   - `methods`

10. **Rendering** (mô tả khai báo về output của component)
    - `template`/`render`

## Thứ tự thuộc tính element {#element-attribute-order}

**Các thuộc tính của element (bao gồm component) nên được sắp xếp theo thứ tự nhất quán.**

Đây là thứ tự mặc định chúng tôi khuyến nghị cho các option component. Chúng được chia thành danh mục để bạn biết nơi thêm thuộc tính và directive tùy chỉnh.

1. **Definition** (cung cấp các option component)

   - `is`

2. **List Rendering** (tạo nhiều biến thể của cùng một element)

   - `v-for`

3. **Conditionals** (element có được render/hiển thị không)

   - `v-if`
   - `v-else-if`
   - `v-else`
   - `v-show`
   - `v-cloak`

4. **Render Modifiers** (thay đổi cách element render)

   - `v-pre`
   - `v-once`

5. **Global Awareness** (yêu cầu kiến thức ngoài component)

   - `id`

6. **Unique Attributes** (thuộc tính yêu cầu giá trị duy nhất)

   - `ref`
   - `key`

7. **Two-Way Binding** (kết hợp binding và event)

   - `v-model`

8. **Other Attributes** (tất cả thuộc tính bound và unbound chưa được chỉ định)

9. **Events** (event listener của component)

   - `v-on`

10. **Content** (ghi đè nội dung của element)
    - `v-html`
    - `v-text`

## Dòng trống trong option của component/instance {#empty-lines-in-component-instance-options}

**Bạn có thể muốn thêm một dòng trống giữa các property nhiều dòng, đặc biệt khi các option không còn vừa màn hình mà không cần cuộn.**

Khi component bắt đầu cảm thấy chật chội hoặc khó đọc, thêm khoảng trắng giữa các property nhiều dòng có thể giúp dễ lướt qua hơn. Trong một số editor như Vim, tùy chọn định dạng như thế này cũng có thể giúp điều hướng bằng bàn phím dễ hơn.

<div class="options-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue() {
    // ...
  },

  inputClasses() {
    // ...
  }
}
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
// Không có khoảng trắng cũng được, miễn là component
// vẫn dễ đọc và điều hướng.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue() {
    // ...
  },
  inputClasses() {
    // ...
  }
}
```

</div>

</div>

<div class="composition-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
defineProps({
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
})
const formattedValue = computed(() => {
  // ...
})
const inputClasses = computed(() => {
  // ...
})
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
defineProps({
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
})

const formattedValue = computed(() => {
  // ...
})

const inputClasses = computed(() => {
  // ...
})
```

</div>

</div>

## Thứ tự các element cấp cao nhất trong Single-File Component {#single-file-component-top-level-element-order}

**[Single-File Component](/guide/scaling-up/sfc) nên luôn sắp xếp các tag `<script>`, `<template>` và `<style>` nhất quán, với `<style>` cuối cùng, vì ít nhất một trong hai tag kia luôn cần thiết.**

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html [ComponentX.vue]
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

```vue-html [ComponentA.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html [ComponentA.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

hoặc

```vue-html  [ComponentA.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>
