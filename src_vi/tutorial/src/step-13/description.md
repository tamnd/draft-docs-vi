# Emit {#emits}

Ngoài việc nhận props, một component con cũng có thể emit sự kiện đến cha:

<div class="composition-api">
<div class="sfc">

```vue
<script setup>
// khai báo các emitted event
const emit = defineEmits(['response'])

// emit với đối số
emit('response', 'hello from child')
</script>
```

</div>

<div class="html">

```js
export default {
  // khai báo các emitted event
  emits: ['response'],
  setup(props, { emit }) {
    // emit với đối số
    emit('response', 'hello from child')
  }
}
```

</div>

</div>

<div class="options-api">

```js
export default {
  // khai báo các emitted event
  emits: ['response'],
  created() {
    // emit với đối số
    this.$emit('response', 'hello from child')
  }
}
```

</div>

Đối số đầu tiên của <span class="options-api">`this.$emit()`</span><span class="composition-api">`emit()`</span> là tên sự kiện. Các đối số bổ sung được truyền đến event listener.

Cha có thể lắng nghe các sự kiện được emit từ con bằng `v-on` - ở đây handler nhận đối số bổ sung từ lần gọi emit của con và gán nó vào state cục bộ:

<div class="sfc">

```vue-html
<ChildComp @response="(msg) => childMsg = msg" />
```

</div>
<div class="html">

```vue-html
<child-comp @response="(msg) => childMsg = msg"></child-comp>
```

</div>

Bây giờ hãy thử tự mình làm trong trình soạn thảo.
