# Kết xuất có điều kiện {#conditional-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/conditional-rendering-in-vue-3" title="Bài học miễn phí về kết xuất có điều kiện trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-conditionals-in-vue" title="Bài học miễn phí về kết xuất có điều kiện trong Vue.js"/>
</div>

<script setup>
import { ref } from 'vue'
const awesome = ref(true)
</script>

## `v-if` {#v-if}

Directive `v-if` được dùng để kết xuất một khối theo điều kiện. Khối đó chỉ được kết xuất khi biểu thức của directive trả về giá trị truthy.

```vue-html
<h1 v-if="awesome">Vue thật tuyệt!</h1>
```

## `v-else` {#v-else}

Bạn có thể dùng directive `v-else` để chỉ định "khối else" cho `v-if`:

```vue-html
<button @click="awesome = !awesome">Chuyển trạng thái</button>

<h1 v-if="awesome">Vue thật tuyệt!</h1>
<h1 v-else>Ôi không 😢</h1>
```

<div class="demo">
  <button @click="awesome = !awesome">Chuyển trạng thái</button>
  <h1 v-if="awesome">Vue thật tuyệt!</h1>
  <h1 v-else>Ôi không 😢</h1>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpFjkEOgjAQRa8ydIMulLA1hegJ3LnqBskAjdA27RQXhHu4M/GEHsEiKLv5mfdf/sBOxux7j+zAuCutNAQOyZtcKNkZbQkGsFjBCJXVHcQBjYUSqtTKERR3dLpDyCZmQ9bjViiezKKgCIGwM21BGBIAv3oireBYtrK8ZYKtgmg5BctJ13WLPJnhr0YQb1Lod7JaS4G8eATpfjMinjTphC8wtg7zcwNKw/v5eC1fnvwnsfEDwaha7w==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpFjj0OwjAMha9iMsEAFWuVVnACNqYsoXV/RJpEqVOQqt6DDYkTcgRSWoplWX7y56fXs6O1u84jixlvM1dbSoXGuzWOIMdCekXQCw2QS5LrzbQLckje6VEJglDyhq1pMAZyHidkGG9hhObRYh0EYWOVJAwKgF88kdFwyFSdXRPBZidIYDWvgqVkylIhjyb4ayOIV3votnXxfwrk2SPU7S/PikfVfsRnGFWL6akCbeD9fLzmK4+WSGz4AA5dYQY=)

</div>

Phần tử `v-else` phải đứng ngay sau một phần tử `v-if` hoặc `v-else-if`, nếu không nó sẽ không được nhận ra.

## `v-else-if` {#v-else-if}

Đúng như tên gọi, `v-else-if` đóng vai trò là "khối else if" cho `v-if`. Bạn cũng có thể nối nhiều `v-else-if` với nhau:

```vue-html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Không phải A/B/C
</div>
```

Tương tự `v-else`, phần tử `v-else-if` phải đứng ngay sau một phần tử `v-if` hoặc `v-else-if`.

## `v-if` trên `<template>` {#v-if-on-template}

Vì `v-if` là một directive, nó phải được gắn vào một phần tử duy nhất. Nhưng nếu ta muốn chuyển trạng thái cho nhiều phần tử thì sao? Trong trường hợp đó, ta có thể dùng `v-if` trên phần tử `<template>`, đóng vai trò như một wrapper vô hình. Kết quả render cuối cùng sẽ không chứa phần tử `<template>`.

```vue-html
<template v-if="ok">
  <h1>Tiêu đề</h1>
  <p>Đoạn 1</p>
  <p>Đoạn 2</p>
</template>
```

`v-else` và `v-else-if` cũng có thể dùng trên `<template>`.

## `v-show` {#v-show}

Một cách khác để hiển thị phần tử theo điều kiện là dùng directive `v-show`. Cách dùng nhìn chung khá giống nhau:

```vue-html
<h1 v-show="ok">Xin chào!</h1>
```

Điểm khác biệt là phần tử có `v-show` sẽ luôn được render và vẫn nằm trong DOM; `v-show` chỉ thay đổi thuộc tính CSS `display` của phần tử đó.

`v-show` không hỗ trợ phần tử `<template>`, và cũng không hoạt động với `v-else`.

## `v-if` vs. `v-show` {#v-if-vs-v-show}

`v-if` là kết xuất có điều kiện "thật sự" vì nó đảm bảo các event listener và component con bên trong khối điều kiện sẽ được hủy và tạo lại đúng cách trong quá trình chuyển trạng thái.

`v-if` cũng **lazy**: nếu điều kiện là false ở lần render đầu tiên, nó sẽ không làm gì cả, khối điều kiện sẽ chưa được render cho tới khi điều kiện trở thành true lần đầu.

Trong khi đó, `v-show` đơn giản hơn nhiều: phần tử luôn được render bất kể điều kiện ban đầu là gì, rồi việc ẩn/hiện được xử lý bằng CSS.

Nói chung, `v-if` có chi phí chuyển trạng thái cao hơn, còn `v-show` có chi phí render ban đầu cao hơn. Vì vậy, hãy ưu tiên `v-show` nếu bạn cần bật tắt một thứ rất thường xuyên, và ưu tiên `v-if` nếu điều kiện đó hiếm khi thay đổi trong lúc chạy.

## `v-if` cùng `v-for` {#v-if-with-v-for}

Khi `v-if` và `v-for` cùng được dùng trên một phần tử, `v-if` sẽ được đánh giá trước. Xem [hướng dẫn về kết xuất danh sách](list#v-for-with-v-if) để biết chi tiết.

::: warning Lưu ý
Bạn **không nên** dùng `v-if` và `v-for` trên cùng một phần tử vì thứ tự ưu tiên ngầm. Hãy xem [hướng dẫn về kết xuất danh sách](list#v-for-with-v-if) để biết chi tiết.
:::
