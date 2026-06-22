# Render danh sách {#list-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/list-rendering-in-vue-3" title="Bài học miễn phí về List Rendering trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-list-rendering-in-vue" title="Bài học miễn phí về List Rendering trong Vue.js"/>
</div>

## `v-for` {#v-for}

Chúng ta có thể dùng directive `v-for` để render một danh sách item dựa trên một array. Directive `v-for` yêu cầu một cú pháp đặc biệt dạng `item in items`, trong đó `items` là array dữ liệu nguồn và `item` là một **alias (biến đại diện)** cho phần tử đang được lặp:

<div class="composition-api">

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>

<div class="options-api">

```js
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="item in items">
  {{ item.message }}
</li>
```

Bên trong scope của `v-for`, các biểu thức template có thể truy cập tất cả các thuộc tính của scope cha. Ngoài ra, `v-for` cũng hỗ trợ một alias thứ hai tùy chọn cho index của item hiện tại:

<div class="composition-api">

```js
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>
<div class="options-api">

```js
data() {
  return {
    parentMessage: 'Parent',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

<script setup>
const parentMessage = 'Parent'
const items = [{ message: 'Foo' }, { message: 'Bar' }]
</script>

<div class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</div>

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpdTsuqwjAQ/ZVDNlFQu5d64bpwJ7g3LopOJdAmIRlFCPl3p60PcDWcM+eV1X8Iq/uN1FrV6RxtYCTiW/gzzvbBR0ZGpBYFbfQ9tEi1ccadvUuM0ERyvKeUmithMyhn+jCSev4WWaY+vZ7HjH5Sr6F33muUhTR8uW0ThTuJua6mPbJEgGSErmEaENedxX3Z+rgxajbEL2DdhR5zOVOdUSIEDOf8M7IULCHsaPgiMa1eK4QcS6rOSkhdfapVeQLQEWnH)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVTssKwjAQ/JUllyr0cS9V0IM3wbvxEOxWAm0a0m0phPy7m1aqhpDsDLMz48XJ2nwaUZSiGp5OWzpKg7PtHUGNjRpbAi8NQK1I7fbrLMkhjc5EJAn4WOXQ0BWHQb2whOS24CSN6qjXhN1Qwt1Dt2kufZ9ASOGXOyvH3GMNCdGdH75VsZVjwGa2VYQRUdVqmLKmdwcpdjEnBW1qnPf8wZIrBQujoff/RSEEyIDZZeGLeCn/dGJyCSlazSZVsUWL8AYme21i)

</div>

Phạm vi biến của `v-for` tương tự như đoạn JavaScript sau:

```js
const parentMessage = 'Parent'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // có thể truy cập scope ngoài `parentMessage`
  // nhưng `item` và `index` chỉ tồn tại bên trong đây
  console.log(parentMessage, item.message, index)
})
```

Lưu ý rằng giá trị của `v-for` tương ứng với chữ ký hàm của callback `forEach`. Thực tế, bạn có thể dùng destructuring cho alias của `v-for` giống như destructuring tham số hàm:

```vue-html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- với index -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

Với `v-for` lồng nhau, phạm vi hoạt động cũng tương tự như các hàm lồng nhau. Mỗi scope `v-for` có thể truy cập scope cha:

```vue-html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

Bạn cũng có thể dùng `of` thay cho `in` để gần với cú pháp iterator của JavaScript:

```vue-html
<div v-for="item of items"></div>
```

## `v-for` với Object {#v-for-with-an-object}

Bạn cũng có thể dùng `v-for` để lặp qua các property của một object. Thứ tự lặp sẽ dựa trên kết quả của `Object.values()`:

<div class="composition-api">

```js
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    myObject: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}
```

</div>

```vue-html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

Bạn cũng có thể cung cấp alias thứ hai cho tên property (key):

```vue-html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Và thêm alias thứ ba cho index:

```vue-html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jjFvgzAQhf/KE0sSCQKpqg7IqRSpQ9WlWycvBC6KW2NbcKaNEP+9B7Tx4nt33917Y3IKYT9ESspE9XVnAqMnjuFZO9MG3zFGdFTVbAbChEvnW2yE32inXe1dz2hv7+dPqhnHO7kdtQPYsKUSm1f/DfZoPKzpuYdx+JAL6cxUka++E+itcoQX/9cO8SzslZoTy+yhODxlxWN2KMR22mmn8jWrpBTB1AZbMc2KVbTyQ56yBkN28d1RJ9uhspFSfNEtFf+GfnZzjP/oOll2NQPjuM4xTftZyIaU5VwuN0SsqMqtWZxUvliq/J4jmX4BTCp08A==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9T8FqwzAM/RWRS1pImnSMHYI3KOwwdtltJ1/cRqXe3Ng4ctYS8u+TbVJjLD3rPelpLg7O7aaARVeI8eS1ozc54M1ZT9DjWQVDMMsBoFekNtucS/JIwQ8RSQI+1/vX8QdP1K2E+EmaDHZQftg/IAu9BaNHGkEP8B2wrFYxgAp0sZ6pn2pAeLepmEuSXDiy7oL9gduXT+3+pW6f631bZoqkJY/kkB6+onnswoDw6owijIhEMByjUBgNU322/lUWm0mZgBX84r1ifz3ettHmupYskjbanedch2XZRcAKTnnvGVIPBpkqGqPTJNGkkaJ5+CiWf4KkfBs=)

</div>

## `v-for` với Range {#v-for-with-a-range}

`v-for` cũng có thể nhận một số nguyên. Trong trường hợp này nó sẽ lặp template số lần tương ứng, dựa trên range `1...n`.

```vue-html
<span v-for="n in 10">{{ n }}</span>
```

Lưu ý rằng `n` bắt đầu từ 1 thay vì 0.

## `v-for` trên `<template>` {#v-for-on-template}

Tương tự `v-if`, bạn cũng có thể dùng `<template>` với `v-for` để render nhiều phần tử:

```vue-html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` với `v-if` {#v-for-with-v-if}

Khi dùng trên cùng một node, `v-if` có độ ưu tiên cao hơn `v-for`. Điều này có nghĩa là `v-if` sẽ không truy cập được biến trong scope của `v-for`:

```vue-html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Điều này có thể sửa bằng cách đưa `v-for` lên `<template>`:

```vue-html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

:::warning Lưu ý
Không nên dùng `v-if` và `v-for` trên cùng một phần tử vì thứ tự ưu tiên ngầm.

Hai trường hợp phổ biến:

* Lọc danh sách → dùng computed
* Ẩn danh sách → đặt `v-if` lên phần tử cha
  :::

## Giữ trạng thái với `key` {#maintaining-state-with-key}

Khi Vue cập nhật danh sách render bằng `v-for`, mặc định nó dùng chiến lược "in-place patch".

Nếu thứ tự dữ liệu thay đổi, Vue không di chuyển DOM mà patch lại từng phần tử.

Cách này hiệu quả, nhưng chỉ phù hợp khi không phụ thuộc state của component con hoặc DOM tạm.

Để Vue theo dõi identity của từng node, cần dùng `key`:

```vue-html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

Với `<template v-for>`:

```vue-html
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

:::tip Lưu ý
`key` ở đây là một thuộc tính đặc biệt được bind với `v-bind`. Không nhầm với biến property key khi [dùng `v-for` với object](#v-for-with-an-object).
:::

Nên dùng `key` khi có thể, trừ khi nội dung DOM được lặp rất đơn giản (không chứa component hoặc phần tử DOM có trạng thái), hoặc bạn đang cố ý dựa vào hành vi mặc định để tăng hiệu năng.

Binding `key` yêu cầu giá trị nguyên thủy - tức là string hoặc number. Không dùng object làm key cho `v-for`. Để biết chi tiết về attribute `key`, xem [tài liệu API `key`](/api/built-in-special-attributes#key).

## `v-for` với Component {#v-for-with-a-component}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua và quay lại sau.

Bạn có thể dùng `v-for` trực tiếp trên component, giống như bất kỳ phần tử thông thường nào (đừng quên cung cấp `key`):

```vue-html
<MyComponent v-for="item in items" :key="item.id" />
```

Tuy nhiên, điều này sẽ không tự động truyền bất kỳ dữ liệu nào vào component, vì component có scope cô lập của riêng nó. Để truyền dữ liệu đã lặp vào component, chúng ta cần dùng props:

```vue-html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

Lý do không tự inject `item` vào component là để tránh tạo sự phụ thuộc chặt chẽ vào cách `v-for` hoạt động. Việc khai báo rõ nguồn gốc dữ liệu giúp component có thể tái sử dụng trong các tình huống khác.

<div class="composition-api">

Xem [ví dụ về danh sách todo đơn giản này](https://play.vuejs.org/#eNp1U8Bu2zAM/RXCGGAHTWx02ylwgxZYB+ywYRhyq3dwLGYRYkuCJTsZjPz7KMmK3ay9JBQfH/meKA/Rk1Jp32G0jnJdtVwZ0Gg6tSkEb5RsDQzQ4h4usG9lAzGVxldoK5n8ZrAZsTQLCduRygAKUUmhDQg8WWyLZwMPtmESx4sAGkL0mH6xrMH+AHC2hvuljw03Na4h/iLBHBAY1wfUbsTFVcwoH28o2/KIIDuaQ0TTlvrwNu/TDe+7PDlKXZ6EZxTiN4kuRI3W0dk4u4yUf7bZfScqw6WAkrEf3m+y8AOcw7Qv6w5T1elDMhs7Nbq7e61gdmme60SQAvgfIhExiSSJeeb3SBukAy1D1aVBezL5XrYN9Csp1rrbNdykqsUehXkookl0EVGxlZHX5Q5rIBLhNHFlbRD6xBiUzlOeuZJQz4XqjI+BxjSSYe2pQWwRBZizV01DmsRWeJA1Qzv0Of2TwldE5hZRlVd+FkbuOmOksJLybIwtkmfWqg+7qz47asXpSiaN3lxikSVwwfC8oD+/sEnV+oh/qcxmU85mebepgLjDBD622Mg+oDrVquYVJm7IEu4XoXKTZ1dho3gnmdJhedEymn9ab3ysDPdc4M9WKp28xE5JbB+rzz/Trm3eK3LAu8/E7p2PNzYM/i3ChR7W7L7hsSIvR7L2Aal1EhqTp80vF95sw3WcG7r8A0XaeME=) để xem cách render danh sách component bằng `v-for`, truyền dữ liệu khác nhau vào mỗi instance.

</div>
<div class="options-api">

Xem [ví dụ về danh sách todo đơn giản này](https://play.vuejs.org/#eNqNVE2PmzAQ/SsjVIlEm4C27Qmx0a7UVuqhPVS5lT04eFKsgG2BSVJF+e8d2xhIu10tihR75s2bNx9wiZ60To49RlmUd2UrtNkUUjRatQa2iquvBhvYt6qBOEmDwQbEhQQoJJ4dlOOe9bWBi7WWiuIlStNlcJlYrivr5MywxdIDAVo0fSvDDUDiyeK3eDYZxLGLsI8hI7H9DHeYQuwjeAb3I9gFCFMjUXxSYCoELroKO6fZP17Mf6jev0i1ZQcE1RtHaFrWVW/l+/Ai3zd1clQ1O8k5Uzg+j1HUZePaSFwfvdGhfNIGTaW47bV3Mc6/+zZOfaaslegS18ZE9121mIm0Ep17ynN3N5M8CB4g44AC4Lq8yTFDwAPNcK63kPTL03HR6EKboWtm0N5MvldtA8e1klnX7xphEt3ikTbpoYimsoqIwJY0r9kOa6Ag8lPeta2PvE+cA3M7k6cOEvBC6n7UfVw3imPtQ8eiouAW/IY0mElsiZWqOdqkn5NfCXxB5G6SJRvj05By1xujpJWUp8PZevLUluqP/ajPploLasmk0Re3sJ4VCMnxvKQ//0JMqrID/iaYtSaCz+xudsHjLpPzscVGHYO3SzpdixIXLskK7pcBucnTUdgg3kkmcxhetIrmH4ebr8m/n4jC6FZp+z7HTlLsVx1p4M7odcXPr6+Lnb8YOne5+C2F6/D6DH2Hx5JqOlCJ7yz7IlBTbZsf7vjXVBzjvLDrH5T0lgo=) để xem cách render danh sách component bằng `v-for`, truyền dữ liệu khác nhau vào mỗi instance.

</div>

## Phát hiện thay đổi Array {#array-change-detection}

### Mutation Methods {#mutation-methods}

Vue có thể phát hiện các method thay đổi array:

* `push()`
* `pop()`
* `shift()`
* `unshift()`
* `splice()`
* `sort()`
* `reverse()`

### Thay thế Array {#replacing-an-array}

Các method trên thay đổi array gốc.

Các method như `filter()`, `concat()`, `slice()` trả về array mới.

Khi dùng chúng, cần gán lại:

<div class="composition-api">

```js
// `items` là một ref với giá trị array
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

</div>
<div class="options-api">

```js
this.items = this.items.filter((item) => item.message.match(/Foo/))
```

</div>

Vue vẫn tối ưu DOM khi replace array.

## Hiển thị danh sách lọc/sắp xếp {#displaying-filtered-sorted-results}

Đôi khi chúng ta muốn hiển thị phiên bản lọc hoặc sắp xếp của một array mà không thực sự mutate hay reset dữ liệu gốc. Trong trường hợp này, bạn có thể tạo một computed property trả về array đã lọc hoặc sắp xếp.

Ví dụ:

<div class="composition-api">

```js
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    numbers: [1, 2, 3, 4, 5]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(n => n % 2 === 0)
  }
}
```

</div>

```vue-html
<li v-for="n in evenNumbers">{{ n }}</li>
```

Trong các tình huống không thể dùng computed property (ví dụ: bên trong vòng lặp `v-for` lồng nhau), bạn có thể dùng method:

<div class="composition-api">

```js
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

</div>

```vue-html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

Cẩn thận với `reverse()` và `sort()` trong computed vì chúng mutate array.

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```

