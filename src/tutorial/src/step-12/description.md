# Props {#props}

Một component con có thể nhận đầu vào từ cha qua **props**. Đầu tiên, nó cần khai báo các props mà nó chấp nhận:

<div class="composition-api">
<div class="sfc">

```vue [ChildComp.vue]
<script setup>
const props = defineProps({
  msg: String
})
</script>
```

Lưu ý `defineProps()` là compile-time macro và không cần được import. Sau khi khai báo, prop `msg` có thể dùng trong template của component con. Nó cũng có thể được truy cập trong JavaScript qua object được trả về của `defineProps()`.

</div>

<div class="html">

```js
// trong component con
export default {
  props: {
    msg: String
  },
  setup(props) {
    // truy cập props.msg
  }
}
```

Sau khi khai báo, prop `msg` được expose trên `this` và có thể dùng trong template của component con. Các props nhận được được truyền vào `setup()` làm đối số đầu tiên.

</div>

</div>

<div class="options-api">

```js
// trong component con
export default {
  props: {
    msg: String
  }
}
```

Sau khi khai báo, prop `msg` được expose trên `this` và có thể dùng trong template của component con.

</div>

Cha có thể truyền prop cho con giống như thuộc tính. Để truyền giá trị động, chúng ta cũng có thể dùng cú pháp `v-bind`:

<div class="sfc">

```vue-html
<ChildComp :msg="greeting" />
```

</div>
<div class="html">

```vue-html
<child-comp :msg="greeting"></child-comp>
```

</div>

Bây giờ hãy thử tự mình làm trong trình soạn thảo.
