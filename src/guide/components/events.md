<script setup>
import { onMounted } from 'vue'

if (typeof window !== 'undefined') {
  const hash = window.location.hash

  // Tài liệu về v-model trước đây từng nằm trong trang này.
  // Thử chuyển hướng các liên kết cũ đã lỗi thời.
  if ([
    '#usage-with-v-model',
    '#v-model-arguments',
    '#multiple-v-model-bindings',
    '#handling-v-model-modifiers'
  ].includes(hash)) {
    onMounted(() => {
      window.location = './v-model.html' + hash
    })
  }
}
</script>

# Sự Kiện Component {#component-events}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/defining-custom-events-emits" title="Bài học miễn phí về khai báo custom event"/>
</div>

## Phát Và Lắng Nghe Sự Kiện {#emitting-and-listening-to-events}

Một component có thể phát custom event trực tiếp trong biểu thức template (ví dụ trong một handler `v-on`) bằng phương thức dựng sẵn `$emit`:

```vue-html
<!-- MyComponent -->
<button @click="$emit('someEvent')">Click Me</button>
```

<div class="options-api">

Phương thức `$emit()` cũng khả dụng trên instance component dưới dạng `this.$emit()`:

```js
export default {
  methods: {
    submit() {
      this.$emit('someEvent')
    }
  }
}
```

</div>

Component cha sau đó có thể lắng nghe bằng `v-on`:

```vue-html
<MyComponent @some-event="callback" />
```

Modifier `.once` cũng được hỗ trợ trên listener của sự kiện component:

```vue-html
<MyComponent @some-event.once="callback" />
```

Giống như tên component và prop, tên sự kiện cũng được tự động chuyển đổi kiểu chữ. Hãy chú ý rằng ta phát một sự kiện camelCase, nhưng vẫn có thể lắng nghe nó bằng listener kebab-case ở component cha. Tương tự như [kiểu chữ của prop](/guide/components/props#prop-name-casing), chúng tôi khuyến nghị dùng listener sự kiện dạng kebab-case trong template.

:::tip
Khác với sự kiện DOM gốc, các sự kiện do component phát ra **không** nổi bọt. Bạn chỉ có thể lắng nghe các sự kiện được phát ra bởi component con trực tiếp. Nếu cần giao tiếp giữa các component cùng cấp hoặc lồng sâu, hãy dùng event bus bên ngoài hoặc [giải pháp quản lý state global](/guide/scaling-up/state-management).
:::

## Đối Số Của Sự Kiện {#event-arguments}

Đôi khi sẽ hữu ích nếu phát kèm một giá trị cụ thể cùng sự kiện. Ví dụ, ta có thể muốn component `<BlogPost>` tự quyết định sẽ tăng cỡ chữ lên bao nhiêu. Trong những trường hợp như vậy, ta có thể truyền thêm đối số vào `$emit` để cung cấp giá trị đó:

```vue-html
<button @click="$emit('increaseBy', 1)">
  Tăng 1
</button>
```

Khi lắng nghe sự kiện này ở component cha, ta có thể dùng hàm mũi tên nội tuyến làm listener, từ đó truy cập được đối số của sự kiện:

```vue-html
<MyButton @increase-by="(n) => count += n" />
```

Hoặc, nếu handler sự kiện là một phương thức:

```vue-html
<MyButton @increase-by="increaseCount" />
```

Khi đó, giá trị sẽ được truyền vào tham số đầu tiên của phương thức đó:

<div class="options-api">

```js
methods: {
  increaseCount(n) {
    this.count += n
  }
}
```

</div>
<div class="composition-api">

```js
function increaseCount(n) {
  count.value += n
}
```

</div>

:::tip
Mọi đối số bổ sung được truyền vào `$emit()` sau tên sự kiện sẽ được chuyển tiếp đến listener. Ví dụ, với `$emit('foo', 1, 2, 3)` thì hàm listener sẽ nhận ba đối số.
:::

## Khai Báo Các Sự Kiện Được Phát Ra {#declaring-emitted-events}

Một component có thể khai báo tường minh những sự kiện mà nó sẽ phát bằng <span class="composition-api">macro [`defineEmits()`](/api/sfc-script-setup#defineprops-defineemits)</span><span class="options-api">option [`emits`](/api/options-state#emits)</span>:

<div class="composition-api">

```vue
<script setup>
defineEmits(['inFocus', 'submit'])
</script>
```

Phương thức `$emit` mà ta dùng trong `<template>` không thể truy cập được bên trong phần `<script setup>` của component, nhưng `defineEmits()` sẽ trả về một hàm tương đương để ta dùng thay thế:

```vue
<script setup>
const emit = defineEmits(['inFocus', 'submit'])

function buttonClick() {
  emit('submit')
}
</script>
```

Macro `defineEmits()` **không thể** được dùng bên trong một hàm; nó phải được đặt trực tiếp trong `<script setup>` như ví dụ trên.

Nếu bạn dùng hàm `setup` tường minh thay vì `<script setup>`, các sự kiện nên được khai báo bằng option [`emits`](/api/options-state#emits), còn hàm `emit` sẽ được expose trong context `setup()`:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, ctx) {
    ctx.emit('submit')
  }
}
```

Giống như các property khác của context `setup()`, `emit` có thể được destructure an toàn:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, { emit }) {
    emit('submit')
  }
}
```

</div>
<div class="options-api">

```js
export default {
  emits: ['inFocus', 'submit']
}
```

</div>

Option `emits` và macro `defineEmits()` cũng hỗ trợ cú pháp object. Nếu dùng TypeScript, bạn có thể gắn kiểu cho đối số, qua đó cho phép kiểm tra payload của sự kiện được phát ra ngay lúc chạy:

<div class="composition-api">

```vue
<script setup lang="ts">
const emit = defineEmits({
  submit(payload: { email: string, password: string }) {
    // trả về `true` hoặc `false` để biểu thị
    // validation pass / fail
  }
})
</script>
```

Nếu bạn dùng TypeScript với `<script setup>`, bạn cũng có thể khai báo sự kiện được phát ra chỉ bằng chú thích kiểu thuần túy:

```vue
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
</script>
```

Chi tiết hơn: [Typing Component Emits](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

</div>
<div class="options-api">

```js
export default {
  emits: {
    submit(payload: { email: string, password: string }) {
      // trả về `true` hoặc `false` để biểu thị
      // validation pass / fail
    }
  }
}
```

Xem thêm: [Typing Component Emits](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

</div>

Dù là tùy chọn, ta vẫn nên định nghĩa đầy đủ các sự kiện được phát ra để tài liệu hóa rõ hơn cách component hoạt động. Điều này cũng cho phép Vue loại bỏ các listener đã biết khỏi [thuộc tính kế thừa](/guide/components/attrs#v-on-listener-inheritance), nhờ đó tránh được các edge case do sự kiện DOM cùng tên bị dispatch thủ công bởi code bên thứ ba.

:::tip
Nếu một sự kiện gốc (ví dụ `click`) được định nghĩa trong option `emits`, listener sẽ chỉ còn lắng nghe các sự kiện `click` do component phát ra và không còn phản ứng với sự kiện `click` DOM gốc nữa.
:::

## Kiểm Tra Tính Hợp Lệ Của Sự Kiện {#events-validation}

Tương tự như kiểm tra kiểu của prop, một sự kiện được phát ra cũng có thể được kiểm tra tính hợp lệ nếu nó được định nghĩa bằng cú pháp object thay vì cú pháp mảng.

Để thêm validation, ta gán cho sự kiện một hàm nhận các đối số được truyền vào lệnh gọi <span class="options-api">`this.$emit`</span><span class="composition-api">`emit`</span> và trả về boolean để cho biết sự kiện đó có hợp lệ hay không.

<div class="composition-api">

```vue
<script setup>
const emit = defineEmits({
  // Không validation
  click: null,

  // Validation sự kiện submit
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```

</div>
<div class="options-api">

```js
export default {
  emits: {
    // Không validation
    click: null,

    // Validation sự kiện submit
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })
    }
  }
}
```

</div>
