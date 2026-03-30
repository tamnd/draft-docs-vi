# Binding Form {#form-bindings}

Dùng `v-bind` và `v-on` cùng nhau, chúng ta có thể tạo binding hai chiều trên các element input của form:

```vue-html
<input :value="text" @input="onInput">
```

<div class="options-api">

```js
methods: {
  onInput(e) {
    // v-on handler nhận native DOM event
    // làm đối số.
    this.text = e.target.value
  }
}
```

</div>

<div class="composition-api">

```js
function onInput(e) {
  // v-on handler nhận native DOM event
  // làm đối số.
  text.value = e.target.value
}
```

</div>

Thử nhập vào ô input - bạn sẽ thấy text trong `<p>` cập nhật khi bạn gõ.

Để đơn giản hóa binding hai chiều, Vue cung cấp directive `v-model`, về cơ bản là syntactic sugar cho những gì ở trên:

```vue-html
<input v-model="text">
```

`v-model` tự động đồng bộ giá trị của `<input>` với state được bind, vì vậy chúng ta không cần dùng event handler cho điều đó nữa.

`v-model` không chỉ hoạt động trên text input, mà còn trên các loại input khác như checkbox, radio button, và select dropdown. Chúng tôi đề cập thêm chi tiết trong <a target="_blank" href="/guide/essentials/forms.html">Hướng dẫn - Binding Form</a>.

Bây giờ, hãy thử refactor code để dùng `v-model` thay thế.
