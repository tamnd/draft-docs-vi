# Quy tắc Ưu tiên A: Thiết yếu {#priority-a-rules-essential}

::: warning Lưu ý
Hướng dẫn phong cách Vue.js này đã lỗi thời và cần được xem xét lại. Nếu bạn có câu hỏi hoặc gợi ý, vui lòng [mở issue](https://github.com/vuejs/docs/issues/new).
:::

Các quy tắc này giúp ngăn ngừa lỗi, vì vậy hãy học và tuân thủ chúng bằng mọi giá. Có thể có ngoại lệ, nhưng phải rất hiếm và chỉ được thực hiện bởi những người có kiến thức chuyên sâu về cả JavaScript và Vue.

## Dùng tên component nhiều từ {#use-multi-word-component-names}

Tên component người dùng nên luôn nhiều từ, ngoại trừ component gốc `App`. Điều này [ngăn xung đột](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) với các element HTML hiện có và tương lai, vì tất cả element HTML đều là một từ.

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<!-- trong template đã biên dịch trước -->
<Item />

<!-- trong template trong DOM -->
<item></item>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<!-- trong template đã biên dịch trước -->
<TodoItem />

<!-- trong template trong DOM -->
<todo-item></todo-item>
```

</div>

## Dùng định nghĩa prop chi tiết {#use-detailed-prop-definitions}

Trong code đã commit, định nghĩa prop nên luôn chi tiết nhất có thể, tối thiểu phải chỉ định kiểu dữ liệu.

::: details Giải thích chi tiết
[Định nghĩa prop](/guide/components/props#prop-validation) chi tiết có hai lợi ích:

- Chúng ghi lại API của component, giúp dễ thấy cách component được thiết kế để dùng.
- Trong development, Vue sẽ cảnh báo nếu component nhận prop sai định dạng, giúp phát hiện nguồn gốc lỗi tiềm ẩn.
  :::

<div class="options-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
// Chỉ chấp nhận khi đang prototype
props: ['status']
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
props: {
  status: String
}
```

```js
// Càng tốt hơn!
props: {
  status: {
    type: String,
    required: true,

    validator: value => {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].includes(value)
    }
  }
}
```

</div>

</div>

<div class="composition-api">

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```js
// Chỉ chấp nhận khi đang prototype
const props = defineProps(['status'])
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```js
const props = defineProps({
  status: String
})
```

```js
// Càng tốt hơn!

const props = defineProps({
  status: {
    type: String,
    required: true,

    validator: (value) => {
      return ['syncing', 'synced', 'version-conflict', 'error'].includes(
        value
      )
    }
  }
})
```

</div>

</div>

## Dùng `v-for` có key {#use-keyed-v-for}

`key` với `v-for` là _bắt buộc_ trên component để duy trì state nội bộ của component trong cây con. Ngay cả với element thông thường, việc dùng key là thực hành tốt để đảm bảo hành vi dự đoán được, ví dụ như [object constancy](https://bost.ocks.org/mike/constancy/) trong animation.

::: details Giải thích chi tiết
Giả sử bạn có danh sách todos:

<div class="options-api">

```js
data() {
  return {
    todos: [
      {
        id: 1,
        text: 'Learn to use v-for'
      },
      {
        id: 2,
        text: 'Learn to use key'
      }
    ]
  }
}
```

</div>

<div class="composition-api">

```js
const todos = ref([
  {
    id: 1,
    text: 'Learn to use v-for'
  },
  {
    id: 2,
    text: 'Learn to use key'
  }
])
```

</div>

Sau đó bạn sắp xếp chúng theo thứ tự alphabet. Khi cập nhật DOM, Vue sẽ tối ưu hóa render để thực hiện ít thao tác DOM nhất có thể. Điều đó có thể có nghĩa là xóa phần tử todo đầu tiên, rồi thêm lại ở cuối danh sách.

Vấn đề là có những trường hợp quan trọng là không xóa các element vẫn còn trong DOM. Ví dụ: bạn có thể muốn dùng `<transition-group>` để animate việc sắp xếp danh sách, hoặc duy trì focus nếu element được render là `<input>`. Trong những trường hợp này, thêm key duy nhất cho mỗi item (ví dụ: `:key="todo.id"`) sẽ cho Vue biết cách hoạt động có thể dự đoán hơn.

Theo kinh nghiệm, tốt hơn là _luôn_ thêm key duy nhất để bạn và nhóm không bao giờ phải lo về các edge case này. Sau đó trong các tình huống hiếm gặp, đòi hỏi hiệu năng cao mà object constancy không cần thiết, bạn có thể tạo ngoại lệ có chủ đích.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

</div>

## Tránh dùng `v-if` với `v-for` {#avoid-v-if-with-v-for}

**Không bao giờ dùng `v-if` trên cùng element với `v-for`.**

Có hai trường hợp phổ biến khiến điều này trở nên hấp dẫn:

- Để lọc items trong danh sách (ví dụ: `v-for="user in users" v-if="user.isActive"`). Trong những trường hợp này, thay `users` bằng computed property mới trả về danh sách đã lọc (ví dụ: `activeUsers`).

- Để tránh render danh sách khi cần ẩn (ví dụ: `v-for="user in users" v-if="shouldShowUsers"`). Trong những trường hợp này, chuyển `v-if` lên element container (ví dụ: `ul`, `ol`).

::: details Giải thích chi tiết
Khi Vue xử lý directive, `v-if` có độ ưu tiên cao hơn `v-for`, nên template này:

```vue-html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Sẽ báo lỗi, vì directive `v-if` được đánh giá trước và biến lặp `user` chưa tồn tại lúc đó.

Có thể khắc phục bằng cách lặp qua computed property:

<div class="options-api">

```js
computed: {
  activeUsers() {
    return this.users.filter(user => user.isActive)
  }
}
```

</div>

<div class="composition-api">

```js
const activeUsers = computed(() => {
  return users.filter((user) => user.isActive)
})
```

</div>

```vue-html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Ngoài ra, có thể dùng tag `<template>` với `v-for` để bọc element `<li>`:

```vue-html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

```vue-html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

</div>

## Dùng styling có phạm vi component {#use-component-scoped-styling}

Đối với ứng dụng, style trong component `App` cấp cao nhất và trong layout component có thể là global, nhưng tất cả component khác nên luôn có phạm vi.

Điều này chỉ liên quan đến [Single-File Component](/guide/scaling-up/sfc). Nó _không_ yêu cầu phải dùng [thuộc tính `scoped`](https://vue-loader.vuejs.org/guide/scoped-css.html). Phạm vi có thể thông qua [CSS modules](https://vue-loader.vuejs.org/guide/css-modules.html), chiến lược dựa trên class như [BEM](http://getbem.com/), hoặc thư viện/quy ước khác.

**Tuy nhiên, thư viện component nên ưu tiên chiến lược dựa trên class thay vì dùng thuộc tính `scoped`.**

Điều này giúp việc ghi đè style nội bộ dễ dàng hơn, với tên class dễ đọc không có độ đặc hiệu quá cao, nhưng vẫn rất khó dẫn đến xung đột.

::: details Giải thích chi tiết
Nếu bạn đang phát triển dự án lớn, làm việc với developer khác, hoặc đôi khi dùng HTML/CSS bên thứ ba (ví dụ: từ Auth0), việc scoping nhất quán sẽ đảm bảo style của bạn chỉ áp dụng cho các component được thiết kế.

Ngoài thuộc tính `scoped`, dùng tên class duy nhất có thể giúp đảm bảo CSS bên thứ ba không áp dụng vào HTML của bạn. Ví dụ: nhiều dự án dùng tên class `button`, `btn`, hoặc `icon`, vì vậy ngay cả khi không dùng chiến lược như BEM, thêm tiền tố đặc thù cho app và/hoặc component (ví dụ: `ButtonClose-icon`) có thể cung cấp một số bảo vệ.
:::

<div class="style-example style-example-bad">
<h3>Không nên</h3>

```vue-html
<template>
  <button class="btn btn-close">×</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```

</div>

<div class="style-example style-example-good">
<h3>Nên</h3>

```vue-html
<template>
  <button class="button button-close">×</button>
</template>

<!-- Dùng thuộc tính `scoped` -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

```vue-html
<template>
  <button :class="[$style.button, $style.buttonClose]">×</button>
</template>

<!-- Dùng CSS modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

```vue-html
<template>
  <button class="c-Button c-Button--close">×</button>
</template>

<!-- Dùng quy ước BEM -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```

</div>
