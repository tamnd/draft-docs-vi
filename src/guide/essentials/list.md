# Kết xuất danh sách {#list-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/list-rendering-in-vue-3" title="Bài học miễn phí về kết xuất danh sách trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-list-rendering-in-vue" title="Bài học miễn phí về kết xuất danh sách trong Vue.js"/>
</div>

## `v-for` {#v-for}

Chúng ta có thể dùng directive `v-for` để kết xuất một danh sách phần tử dựa trên một mảng. Directive `v-for` cần một cú pháp đặc biệt theo dạng `item in items`, trong đó `items` là mảng dữ liệu nguồn và `item` là **bí danh** cho phần tử của mảng đang được lặp qua:

<div class="composition-api">

```js
const items = ref([{ message: 'Một' }, { message: 'Hai' }])
```

</div>

<div class="options-api">

```js
data() {
  return {
    items: [{ message: 'Một' }, { message: 'Hai' }]
  }
}
```

</div>

```vue-html
<li v-for="item in items">
  {{ item.message }}
</li>
```

Bên trong phạm vi của `v-for`, các biểu thức template có thể truy cập mọi property ở phạm vi cha. Ngoài ra, `v-for` cũng hỗ trợ một bí danh thứ hai, không bắt buộc, cho chỉ số của phần tử hiện tại:

<div class="composition-api">

```js
const parentMessage = ref('Cha')
const items = ref([{ message: 'Một' }, { message: 'Hai' }])
```

</div>
<div class="options-api">

```js
data() {
  return {
    parentMessage: 'Cha',
    items: [{ message: 'Một' }, { message: 'Hai' }]
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
const parentMessage = 'Cha'
const items = [{ message: 'Một' }, { message: 'Hai' }]
</script>
<div class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</div>

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpdTsuqwjAQ/ZVDNlFQu5d64bpwJ7g3LopOJdAmIRlFCPl3p60PcDWcM+eV1X8Iq/uN1FrV6RxtYCTiW/gzzvbBR0ZGpBYFbfQ9tEi1ccadvUuM0ERyvKeUmithMyhn+jCSev4WWaY+vZ7HjH5Sr6F33muUhTR8uW0ThTuJua6mPbJEgGSErmEaENedxX3Z+rgxajbEL2DdhR5zOVOdUSIEDOf8M7IULCHsaPgiMa1eK4QcS6rOSkhdfapVeQLQEWnH)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpVTssKwjAQ/JUllyr0cS9V0IM3wbvxEOxWAm0a0m0phPy7m1aqhpDsDLMz48XJ2nwaUZSiGp5OWzpKg7PtHUGNjRpbAi8NQK1I7fbrLMkhjc5EJAn4WOXQ0BWHQb2whOS24CSN6qjXhN1Qwt1Dt2kufZ9ASOGXOyvH3GMNCdGdH75VsZVjwGa2VYQRUdVqmLKmdwcpdjEnBW1qnPf8wZIrBQujoff/RSEEyIDZZeGLeCn/dGJyCSlazSZVsUWL8AYme21i)

</div>

Phạm vi biến của `v-for` khá giống với JavaScript sau:

```js
const parentMessage = 'Cha'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // có thể truy cập phạm vi bên ngoài là `parentMessage`
  // nhưng `item` và `index` chỉ có sẵn bên trong hàm này
  console.log(parentMessage, item.message, index)
})
```

Hãy để ý cách giá trị của `v-for` khớp với chữ ký hàm của callback `forEach`. Thực tế, bạn cũng có thể dùng destructuring cho bí danh item trong `v-for`, tương tự như destructuring tham số hàm:

```vue-html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- kèm bí danh chỉ số -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

Với `v-for` lồng nhau, phạm vi biến cũng hoạt động tương tự các hàm lồng nhau. Mỗi phạm vi `v-for` đều có thể truy cập các phạm vi cha:

```vue-html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

Bạn cũng có thể dùng `of` thay cho `in`, để cú pháp gần hơn với cách viết iterator trong JavaScript:

```vue-html
<div v-for="item of items"></div>
```

## `v-for` với object {#v-for-with-an-object}

Bạn cũng có thể dùng `v-for` để lặp qua các property của một object. Thứ tự lặp sẽ dựa trên kết quả trả về của `Object.values()` khi gọi trên object đó:

<div class="composition-api">

```js
const myObject = reactive({
  title: 'Cách làm danh sách trong Vue',
  author: 'Nguyen Van A',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    myObject: {
      title: 'Cách làm danh sách trong Vue',
      author: 'Nguyen Van A',
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

Bạn cũng có thể cung cấp bí danh thứ hai cho tên property, hay còn gọi là key:

```vue-html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Và thêm một bí danh nữa cho chỉ số:

```vue-html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9jjFvgzAQhf/KE0sSCQKpqg7IqRSpQ9WlWycvBC6KW2NbcKaNEP+9B7Tx4nt33917Y3IKYT9ESspE9XVnAqMnjuFZO9MG3zFGdFTVbAbChEvnW2yE32inXe1dz2hv7+dPqhnHO7kdtQPYsKUSm1f/DfZoPKzpuYdx+JAL6cxUka++E+itcoQX/9cO8SzslZoTy+yhODxlxWN2KMR22mmn8jWrpBTB1AZbMc2KVbTyQ56yBkN28d1RJ9uhspFSfNEtFf+GfnZzjP/oOll2NQPjuM4xTftZyIaU5VwuN0SsqMqtWZxUvliq/J4jmX4BTCp08A==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNo9T8FqwzAM/RWRS1pImnSMHYI3KOwwdtltJ1/cRqXe3Ng4ctYS8u+TbVJjLD3rPelpLg7O7aaARVeI8eS1ozc54M1ZT9DjWQVDMMsBoFekNtucS/JIwQ8RSQI+1/vX8QdP1K2E+EmaDHZQftg/IAu9BaNHGkEP8B2wrFYxgAp0sZ6pn2pAeLepmEuSXDiy7oL9gduXT+3+pW6f631bZoqkJY/kkB6+onnswoDw6owijIhEMByjUBgNU322/lUWm0mZgBX84r1ifz3ettHmupYskjbanedch2XZRcAKTnnvGVIPBpkqGqPTJNGkkaJ5+CiWf4KkfBs=)

</div>

## `v-for` với một khoảng giá trị {#v-for-with-a-range}

`v-for` cũng có thể nhận vào một số nguyên. Trong trường hợp này, nó sẽ lặp lại template đúng số lần tương ứng, dựa trên khoảng `1...n`.

```vue-html
<span v-for="n in 10">{{ n }}</span>
```

Lưu ý rằng ở đây `n` bắt đầu từ `1` chứ không phải `0`.

## `v-for` trên `<template>` {#v-for-on-template}

Tương tự như `v-if` trên template, bạn cũng có thể dùng thẻ `<template>` với `v-for` để kết xuất một khối gồm nhiều phần tử. Ví dụ:

```vue-html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` cùng `v-if` {#v-for-with-v-if}

Khi cùng xuất hiện trên một node, `v-if` có độ ưu tiên cao hơn `v-for`. Điều đó có nghĩa là điều kiện của `v-if` sẽ không thể truy cập những biến trong phạm vi của `v-for`:

```vue-html
<!--
Đoạn này sẽ gây lỗi vì property "todo"
không được định nghĩa trên instance.
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Có thể sửa bằng cách chuyển `v-for` sang một thẻ `<template>` bao ngoài, cách này cũng rõ nghĩa hơn:

```vue-html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

:::warning Lưu ý
Bạn **không nên** dùng `v-if` và `v-for` trên cùng một phần tử vì thứ tự ưu tiên ngầm.

Có hai trường hợp thường khiến bạn muốn làm như vậy:

- Để lọc phần tử trong một danh sách, ví dụ `v-for="user in users" v-if="user.isActive"`. Trong trường hợp này, hãy thay `users` bằng một computed property mới trả về danh sách đã được lọc, ví dụ `activeUsers`.

- Để tránh render một danh sách nếu nó cần được ẩn đi, ví dụ `v-for="user in users" v-if="shouldShowUsers"`. Trong trường hợp này, hãy chuyển `v-if` lên một phần tử chứa bên ngoài, ví dụ `ul` hoặc `ol`.
:::

## Duy trì state với `key` {#maintaining-state-with-key}

Khi Vue cập nhật một danh sách phần tử được kết xuất bằng `v-for`, mặc định nó dùng chiến lược "vá tại chỗ" (in-place patch). Nếu thứ tự của các phần tử dữ liệu thay đổi, thay vì di chuyển DOM element để khớp với thứ tự mới, Vue sẽ vá từng phần tử ngay tại vị trí hiện có và đảm bảo nó phản ánh đúng nội dung cần render ở chỉ số đó.

Chế độ mặc định này hoạt động hiệu quả, nhưng **chỉ phù hợp khi kết quả render của danh sách không phụ thuộc vào state của component con hoặc state DOM tạm thời, chẳng hạn như giá trị trong ô input của form**.

Để giúp Vue nhận diện từng node, từ đó có thể tái sử dụng và sắp xếp lại các phần tử đang có, bạn cần cung cấp một thuộc tính `key` duy nhất cho mỗi item:

```vue-html
<div v-for="item in items" :key="item.id">
  <!-- nội dung -->
</div>
```

Khi dùng `<template v-for>`, `key` nên được đặt trên phần tử chứa `<template>`:

```vue-html
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

:::tip Lưu ý
`key` ở đây là một thuộc tính đặc biệt được bind bằng `v-bind`. Đừng nhầm nó với key của property khi [dùng `v-for` với object](#v-for-with-an-object).
:::

Bạn nên cung cấp thuộc tính `key` cho `v-for` bất cứ khi nào có thể, trừ khi nội dung DOM được lặp rất đơn giản, nghĩa là không chứa component hay phần tử DOM có state, hoặc bạn cố tình dựa vào hành vi mặc định để tối ưu hiệu năng.

Ràng buộc `key` mong đợi những giá trị nguyên thủy, tức là chuỗi hoặc số. Không nên dùng object làm `v-for` key. Để biết cách dùng chi tiết của thuộc tính `key`, hãy xem [tài liệu API của `key`](/api/built-in-special-attributes#key).

## `v-for` với component {#v-for-with-a-component}

> Phần này giả định rằng bạn đã biết về [Component](/guide/essentials/component-basics). Nếu chưa, bạn có thể tạm bỏ qua và quay lại sau.

Bạn có thể dùng trực tiếp `v-for` trên một component, giống như với phần tử thông thường, chỉ cần nhớ thêm `key`:

```vue-html
<MyComponent v-for="item in items" :key="item.id" />
```

Tuy nhiên, cách này sẽ không tự động truyền dữ liệu nào vào component, vì component có phạm vi riêng tách biệt. Muốn truyền dữ liệu đang được lặp vào component, ta cần dùng thêm props:

```vue-html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

Lý do Vue không tự động đưa `item` vào component là vì làm vậy sẽ khiến component bị gắn chặt với cách `v-for` hoạt động. Việc chỉ rõ dữ liệu đến từ đâu giúp component có thể tái sử dụng trong những tình huống khác.

<div class="composition-api">

Xem [ví dụ về một danh sách todo đơn giản này](https://play.vuejs.org/#eNp1U8Fu2zAM/RXCGGAHTWx02ylwgxZYB+ywYRhyq3dwLGYRYkuCJTsZjPz7KMmK3ay9JBQfH/meKA/Rk1Jp32G0jnJdtVwZ0Gg6tSkEb5RsDQzQ4h4usG9lAzGVxldoK5n8ZrAZsTQLCduRygAKUUmhDQg8WWyLZwMPtmESx4sAGkL0mH6xrMH+AHC2hvuljw03Na4h/iLBHBAY1wfUbsTFVcwoH28o2/KIIDuaQ0TTlvrwNu/TDe+7PDlKXZ6EZxTiN4kuRI3W0dk4u4yUf7bZfScqw6WAkrEf3m+y8AOcw7Qv6w5T1elDMhs7Nbq7e61gdmme60SQAvgfIhExiSSJeeb3SBukAy1D1aVBezL5XrYN9Csp1rrbNdykqsUehXkookl0EVGxlZHX5Q5rIBLhNHFlbRD6xBiUzlOeuZJQz4XqjI+BxjSSYe2pQWwRBZizV01DmsRWeJA1Qzv0Of2TwldE5hZRlVd+FkbuOmOksJLybIwtkmfWqg+7qz47asXpSiaN3lxikSVwwfC8oD+/sEnV+oh/qcxmU85mebepgLjDBD622Mg+oDrVquYVJm7IEu4XoXKTZ1dho3gnmdJhedEymn9ab3ysDPdc4M9WKp28xE5JbB+rzz/Trm3eK3LAu8/E7p2PNzYM/i3ChR7W7L7hsSIvR7L2Aal1EhqTp80vF95sw3WcG7r8A0XaeME=) để xem cách kết xuất một danh sách component bằng `v-for` và truyền dữ liệu khác nhau vào từng instance.

</div>
<div class="options-api">

Xem [ví dụ về một danh sách todo đơn giản này](https://play.vuejs.org/#eNqNVE2PmzAQ/SsjVIlEm4C27Qmx0a7UVuqhPVS5lT04eFKsgG2BSVJF+e8d2xhIu10tihR75s2bNx9wiZ60To49RlmUd2UrtNkUUjRatQa2iquvBhvYt6qBOEmDwQbEhQQoJJ4dlOOe9bWBi7WWiuIlStNlcJlYrivr5MywxdIDAVo0fSvDDUDiyeK3eDYZxLGLsI8hI7H9DHeYQuwjeAb3I9gFCFMjUXxSYCoELroKO6fZP17Mf6jev0i1ZQcE1RtHaFrWVW/l+/Ai3zd1clQ1O8k5Uzg+j1HUZePaSFwfvdGhfNIGTaW47bV3Mc6/+zZOfaaslegS18ZE9121mIm0Ep17ynN3N5M8CB4g44AC4Lq8yTFDwAPNcK63kPTL03HR6EKboWtm0N5MvldtA8e1klnX7xphEt3ikTbpoYimsoqIwJY0r9kOa6Ag8lPeta2PvE+cA3M7k6cOEvBC6n7UfVw3imPtQ8eiouAW/IY0mElsiZWqOdqkn5NfCXxB5G6SJRvj05By1xujpJWUp8PZevLUluqP/ajPploLasmk0Re3sJ4VCMnxvKQ//0JMqrID/iaYtSaCz+xudsHjLpPzscVGHYO3SzpdixIXLskK7pcBucnTUdgg3kkmcxhetIrmH4ebr8m/n4jC6FZp+z7HTlLsVx1p4M7odcXPr6+Lnb8YOne5+C2F6/D6DH2Hx5JqOlCJ7yz7IlBTbZsf7vjXVBzjvLDrH5T0lgo=) để xem cách kết xuất một danh sách component bằng `v-for` và truyền dữ liệu khác nhau vào từng instance.

</div>

## Phát hiện thay đổi của mảng {#array-change-detection}

### Phương thức thay đổi tại chỗ {#mutation-methods}

Vue có thể phát hiện khi các phương thức thay đổi tại chỗ của một mảng phản ứng được gọi, và kích hoạt các cập nhật cần thiết. Những phương thức đó gồm:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### Thay thế một mảng {#replacing-an-array}

Đúng như tên gọi, các phương thức thay đổi tại chỗ sẽ sửa trực tiếp mảng gốc mà chúng được gọi trên đó. Ngược lại, cũng có những phương thức không làm thay đổi mảng gốc như `filter()`, `concat()` và `slice()`. Chúng **luôn trả về một mảng mới**. Khi làm việc với những phương thức này, chúng ta nên thay mảng cũ bằng mảng mới:

<div class="composition-api">

```js
// `items` là một ref chứa giá trị là mảng
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

</div>
<div class="options-api">

```js
this.items = this.items.filter((item) => item.message.match(/Foo/))
```

</div>

Bạn có thể nghĩ rằng cách này sẽ khiến Vue bỏ toàn bộ DOM hiện có và render lại cả danh sách, nhưng may là không phải vậy. Vue triển khai một số chiến lược tối ưu khá thông minh để tái sử dụng DOM element nhiều nhất có thể, nên việc thay một mảng bằng mảng khác có chứa những object trùng nhau là một thao tác rất hiệu quả.

## Hiển thị kết quả đã lọc hoặc sắp xếp {#displaying-filtered-sorted-results}

Đôi khi chúng ta muốn hiển thị một phiên bản đã được lọc hoặc sắp xếp của mảng mà không thực sự thay đổi hay đặt lại dữ liệu gốc. Trong trường hợp này, bạn có thể tạo một computed property trả về mảng đã được lọc hoặc sắp xếp.

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

Trong những tình huống không tiện dùng computed property, chẳng hạn bên trong các vòng `v-for` lồng nhau, bạn có thể dùng method:

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

Hãy cẩn thận với `reverse()` và `sort()` bên trong computed property. Hai phương thức này sẽ làm thay đổi mảng gốc, điều mà bạn nên tránh trong computed getter. Hãy tạo một bản sao của mảng gốc trước khi gọi chúng:

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```
