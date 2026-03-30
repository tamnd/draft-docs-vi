# Quy tắc Ưu tiên D: Dùng Cẩn thận {#priority-d-rules-use-with-caution}

::: warning Lưu ý
Hướng dẫn phong cách Vue.js này đã lỗi thời và cần được xem xét lại. Nếu bạn có câu hỏi hoặc gợi ý, vui lòng [mở issue](https://github.com/vuejs/docs/issues/new).
:::

Một số tính năng của Vue tồn tại để hỗ trợ các trường hợp ngoại lệ hiếm gặp hoặc migration mượt mà hơn từ codebase cũ. Tuy nhiên khi lạm dụng, chúng có thể làm code khó bảo trì hơn hoặc thậm chí trở thành nguồn gốc của lỗi. Các quy tắc này làm nổi bật những tính năng tiềm ẩn rủi ro, mô tả khi nào và tại sao nên tránh chúng.

## Selector element với `scoped` {#element-selectors-with-scoped}

**Nên tránh selector element với `scoped`.**

Ưu tiên selector class hơn selector element trong style `scoped`, vì số lượng lớn selector element sẽ chậm.

::: details Giải thích chi tiết
Để scoping style, Vue thêm thuộc tính duy nhất vào các element component, ví dụ như `data-v-f3f3eg9`. Sau đó các selector được chỉnh sửa để chỉ chọn các element phù hợp có thuộc tính này (ví dụ: `button[data-v-f3f3eg9]`).

Vấn đề là số lượng lớn selector element-attribute (ví dụ: `button[data-v-f3f3eg9]`) sẽ chậm hơn đáng kể so với selector class-attribute (ví dụ: `.btn-close[data-v-f3f3eg9]`), vì vậy nên ưu tiên selector class bất cứ khi nào có thể.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<template>
  <button>×</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<template>
  <button class="btn btn-close">×</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```

</div>

## Giao tiếp ngầm giữa parent và child {#implicit-parent-child-communication}

**Nên ưu tiên prop và event cho giao tiếp giữa component parent và child, thay vì dùng `this.$parent` hoặc thay đổi trực tiếp prop.**

Ứng dụng Vue lý tưởng là prop xuống, event lên. Tuân theo quy ước này giúp component dễ hiểu hơn nhiều. Tuy nhiên, có các edge case mà việc thay đổi prop hoặc dùng `this.$parent` có thể đơn giản hóa hai component đã gắn kết chặt chẽ với nhau.

Vấn đề là cũng có nhiều trường hợp _đơn giản_ mà các pattern này có vẻ thuận tiện. Hãy cẩn thận: đừng bị cám dỗ đánh đổi sự đơn giản (khả năng hiểu flow của state) để lấy tiện ích ngắn hạn (viết ít code hơn).

<div class="options-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  template: '<input v-model="todo.text">'
})
```

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  methods: {
    removeTodo() {
      this.$parent.todos = this.$parent.todos.filter(
        (todo) => todo.id !== vm.todo.id
      )
    }
  },

  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        ×
      </button>
    </span>
  `
})
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  emits: ['input'],

  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

```js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  emits: ['delete'],

  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        ×
      </button>
    </span>
  `
})
```

</div>

</div>

<div class="composition-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue
<script setup>
defineProps({
  todo: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <input v-model="todo.text" />
</template>
```

```vue
<script setup>
import { getCurrentInstance } from 'vue'

const props = defineProps({
  todo: {
    type: Object,
    required: true
  }
})

const instance = getCurrentInstance()

function removeTodo() {
  const parent = instance.parent
  if (!parent) return

  parent.props.todos = parent.props.todos.filter((todo) => {
    return todo.id !== props.todo.id
  })
}
</script>

<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo">×</button>
  </span>
</template>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue
<script setup>
defineProps({
  todo: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['input'])
</script>

<template>
  <input :value="todo.text" @input="emit('input', $event.target.value)" />
</template>
```

```vue
<script setup>
defineProps({
  todo: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['delete'])
</script>

<template>
  <span>
    {{ todo.text }}
    <button @click="emit('delete')">×</button>
  </span>
</template>
```

</div>

</div>
