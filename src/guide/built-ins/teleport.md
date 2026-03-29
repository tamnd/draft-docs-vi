# Teleport {#teleport}

 <VueSchoolLink href="https://vueschool.io/lessons/vue-3-teleport" title="Bài học miễn phí về Teleport trong Vue.js"/>

`<Teleport>` là một component dựng sẵn, cho phép ta "dịch chuyển" một phần template của component sang một node DOM nằm ngoài cấu trúc DOM của chính component đó.

## Cách dùng cơ bản {#basic-usage}

Đôi khi một phần template của component về mặt logic thì thuộc về component đó, nhưng về mặt hiển thị thì nó lại nên xuất hiện ở một nơi khác trong DOM, thậm chí nằm ngoài cả ứng dụng Vue.

Ví dụ phổ biến nhất là khi xây dựng một modal toàn màn hình. Lý tưởng nhất là code cho nút mở modal và bản thân modal nên được viết trong cùng một single-file component, vì cả hai đều gắn với trạng thái mở / đóng của modal. Nhưng điều đó cũng có nghĩa là modal sẽ được render cạnh nút bấm, nằm sâu trong cấu trúc DOM của ứng dụng. Điều này có thể tạo ra một số vấn đề khó chịu khi định vị modal bằng CSS.

Hãy xem cấu trúc HTML sau:

```vue-html
<div class="outer">
  <h3>Ví dụ Vue Teleport</h3>
  <div>
    <MyModal />
  </div>
</div>
```

Và đây là cách cài đặt `<MyModal>`:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const open = ref(false)
</script>

<template>
  <button @click="open = true">Mở modal</button>

  <div v-if="open" class="modal">
    <p>Xin chào từ modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      open: false
    }
  }
}
</script>

<template>
  <button @click="open = true">Mở modal</button>

  <div v-if="open" class="modal">
    <p>Xin chào từ modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>

Component này chứa một `<button>` để mở modal, và một `<div>` với class `.modal`, nơi chứa nội dung modal cùng một nút để tự đóng.

Khi dùng component này trong cấu trúc HTML ban đầu, sẽ có một số vấn đề tiềm ẩn:

- `position: fixed` chỉ đặt phần tử theo viewport khi không có phần tử tổ tiên nào có `transform`, `perspective` hoặc `filter`. Ví dụ, nếu ta muốn animate phần tử tổ tiên `<div class="outer">` bằng CSS transform, bố cục của modal sẽ bị vỡ.
- `z-index` của modal bị giới hạn bởi các phần tử bao quanh nó. Nếu có một phần tử khác chồng lên `<div class="outer">` và có `z-index` cao hơn, nó sẽ che mất modal.

`<Teleport>` cung cấp một cách sạch sẽ để vượt ra khỏi cấu trúc DOM lồng nhau này. Hãy sửa `<MyModal>` để dùng `<Teleport>`:

```vue-html{3,8}
<button @click="open = true">Mở modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Xin chào từ modal!</p>
    <button @click="open = false">Đóng</button>
  </div>
</Teleport>
```

Mục tiêu `to` của `<Teleport>` nhận vào một chuỗi selector CSS hoặc một node DOM thực tế. Ở đây, về bản chất ta đang bảo Vue hãy "**dịch chuyển** phần template này **tới** thẻ **`body`**".

Bạn có thể bấm nút bên dưới và inspect thẻ `<body>` bằng devtools của trình duyệt:

<script setup>
import { ref } from 'vue'
const open = ref(false)
</script>

<div class="demo">
  <button @click="open = true">Mở modal</button>
  <ClientOnly>
    <Teleport to="body">
      <div v-if="open" class="demo modal-demo">
        <p style="margin-bottom:20px">Xin chào từ modal!</p>
        <button @click="open = false">Đóng</button>
      </div>
    </Teleport>
  </ClientOnly>
</div>

<style>
.modal-demo {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
  background-color: var(--vt-c-bg);
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}
</style>

Bạn có thể kết hợp `<Teleport>` với [`<Transition>`](./transition) để tạo modal có animation, xem [ví dụ tại đây](/examples/#modal).

:::tip
Mục tiêu `to` của teleport phải có sẵn trong DOM vào thời điểm `<Teleport>` được mount. Lý tưởng nhất, đó nên là một phần tử nằm ngoài toàn bộ ứng dụng Vue. Nếu bạn nhắm tới một phần tử khác cũng do Vue render, bạn cần bảo đảm phần tử đó được mount trước `<Teleport>`. Nếu đang dùng SSR, hãy xem [Xử lý Teleport trong SSR](/guide/scaling-up/ssr#teleports).
:::

## Dùng cùng với component {#using-with-components}

`<Teleport>` chỉ thay đổi cấu trúc DOM được render, nó không ảnh hưởng tới cấu trúc logic của component. Nói cách khác, nếu bên trong `<Teleport>` có một component, thì component đó vẫn là component con về mặt logic của component cha đang chứa `<Teleport>`. Việc truyền prop và phát event vẫn hoạt động y hệt như cũ.

Điều này cũng có nghĩa là injection từ component cha vẫn hoạt động đúng như mong đợi, và component con đó vẫn sẽ nằm dưới component cha trong Vue Devtools, thay vì bị đặt ở nơi mà nội dung thực tế đã được chuyển tới.

## Tắt Teleport {#disabling-teleport}

Trong một số trường hợp, ta có thể muốn tắt `<Teleport>` theo điều kiện. Ví dụ, ta có thể muốn render một component như lớp phủ trên desktop, nhưng hiển thị inline trên mobile. `<Teleport>` hỗ trợ prop `disabled`, và ta có thể bật tắt nó động:

```vue-html
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

Sau đó, ta có thể cập nhật `isMobile` một cách động.

## Nhiều Teleport dùng chung một đích {#multiple-teleports-on-the-same-target}

Một trường hợp dùng phổ biến là component `<Modal>` có thể tái sử dụng, với khả năng có nhiều instance cùng hoạt động tại một thời điểm. Với kiểu tình huống này, nhiều component `<Teleport>` có thể mount nội dung của chúng vào cùng một phần tử đích. Thứ tự sẽ đơn giản là nối thêm vào sau, nghĩa là component mount sau sẽ nằm sau component mount trước, nhưng tất cả đều nằm trong cùng phần tử đích.

Với cách dùng như sau:

```vue-html
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

Kết quả render sẽ là:

```html
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

## Deferred Teleport <sup class="vt-badge" data-text="3.5+" /> {#deferred-teleport}

Trong Vue 3.5 trở lên, ta có thể dùng prop `defer` để trì hoãn việc resolve đích của Teleport cho tới khi những phần khác của ứng dụng đã được mount. Nhờ vậy, Teleport có thể nhắm tới một phần tử chứa cũng do Vue render, nhưng nằm ở phần muộn hơn trong cây component:

```vue-html
<Teleport defer to="#late-div">...</Teleport>

<!-- ở một vị trí muộn hơn trong template -->
<div id="late-div"></div>
```

Lưu ý là phần tử đích phải được render trong cùng một tick mount / update với Teleport. Nói cách khác, nếu `<div>` đó chỉ được mount sau một giây nữa thì Teleport vẫn sẽ báo lỗi. `defer` hoạt động khá giống với hook vòng đời `mounted`.

---

**Liên quan**

- [API reference của `<Teleport>`](/api/built-in-components#teleport)
- [Xử lý Teleport trong SSR](/guide/scaling-up/ssr#teleports)
