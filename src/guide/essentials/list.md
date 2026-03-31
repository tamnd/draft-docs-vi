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

```js id="a1m9wx"
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>

<div class="options-api">

```js id="z3c8jk"
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html id="w1s9bz"
<li v-for="item in items">
  {{ item.message }}
</li>
```

Bên trong scope của `v-for`, các biểu thức template có thể truy cập tất cả các thuộc tính của scope cha. Ngoài ra, `v-for` cũng hỗ trợ một alias thứ hai tùy chọn cho index của item hiện tại:

<div class="composition-api">

```js id="r9k3nf"
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>
<div class="options-api">

```js id="g7v2ap"
data() {
  return {
    parentMessage: 'Parent',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html id="u6x2dp"
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

```js id="f2j9dp"
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

```vue-html id="k3h2ls"
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- với index -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

Với `v-for` lồng nhau, phạm vi hoạt động cũng tương tự như các hàm lồng nhau. Mỗi scope `v-for` có thể truy cập scope cha:

```vue-html id="p9d2as"
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

Bạn cũng có thể dùng `of` thay cho `in` để gần với cú pháp iterator của JavaScript:

```vue-html id="l7a4nz"
<div v-for="item of items"></div>
```

## `v-for` với Object {#v-for-with-an-object}

Bạn cũng có thể dùng `v-for` để lặp qua các property của một object. Thứ tự lặp sẽ dựa trên kết quả của `Object.values()`:

<div class="composition-api">

```js id="y1v7mt"
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js id="q9x1lc"
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

```vue-html id="t2m8xk"
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

Bạn cũng có thể cung cấp alias thứ hai cho tên property (key):

```vue-html id="d5z3pr"
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Và thêm alias thứ ba cho index:

```vue-html id="n4b7ys"
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

```vue-html id="f1s2an"
<span v-for="n in 10">{{ n }}</span>
```

Lưu ý rằng `n` bắt đầu từ 1 thay vì 0.

## `v-for` trên `<template>` {#v-for-on-template}

Tương tự `v-if`, bạn cũng có thể dùng `<template>` với `v-for` để render nhiều phần tử:

```vue-html id="u3h7ko"
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` với `v-if` {#v-for-with-v-if}

Khi dùng trên cùng một node, `v-if` có độ ưu tiên cao hơn `v-for`. Điều này có nghĩa là `v-if` sẽ không truy cập được biến trong scope của `v-for`:

```vue-html id="p9a1ze"
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Điều này có thể sửa bằng cách đưa `v-for` lên `<template>`:

```vue-html id="h6j2dv"
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

```vue-html id="n8m1qz"
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

Với `<template v-for>`:

```vue-html id="b7d2os"
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

Nên dùng `key` khi có thể, trừ khi nội dung đơn giản.

`key` phải là giá trị primitive (string, number), không dùng object.

## `v-for` với Component {#v-for-with-a-component}

Bạn có thể dùng `v-for` trực tiếp trên component:

```vue-html id="v8m1eq"
<MyComponent v-for="item in items" :key="item.id" />
```

Nhưng dữ liệu không tự truyền vào component.

Cần dùng props:

```vue-html id="k2c9ap"
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

Việc không inject tự động giúp component tái sử dụng tốt hơn.

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

```js id="r5t1zm"
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

Vue vẫn tối ưu DOM khi replace array.

## Hiển thị danh sách lọc/sắp xếp {#displaying-filtered-sorted-results}

Có thể dùng computed để tạo danh sách lọc:

```js id="k1x2bn"
const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

```vue-html id="d9q7ls"
<li v-for="n in evenNumbers">{{ n }}</li>
```

Nếu không dùng computed, có thể dùng method:

```js id="z3p8rm"
function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

```vue-html id="y4s2fv"
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

Cẩn thận với `reverse()` và `sort()` trong computed vì chúng mutate array.

```diff id="f8l3dn"
- return numbers.reverse()
+ return [...numbers].reverse()
```

