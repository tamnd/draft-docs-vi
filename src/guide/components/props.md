# Props {#props}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-3-reusable-components-with-props" title="Bài học miễn phí về props trong Vue.js"/>
</div>

## Khai Báo Props {#props-declaration}

Component Vue yêu cầu props phải được khai báo tường minh để Vue biết những prop bên ngoài nào được truyền vào component cần được xem là thuộc tính kế thừa (sẽ được bàn trong [phần riêng](/guide/components/attrs)).

<div class="composition-api">

Trong SFC dùng `<script setup>`, props có thể được khai báo bằng macro `defineProps()`:

```vue
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

Trong component không dùng `<script setup>`, props được khai báo bằng option [`props`](/api/options-state#props):

```js
export default {
  props: ['foo'],
  setup(props) {
    // setup() nhận props làm đối số đầu tiên.
    console.log(props.foo)
  }
}
```

Hãy chú ý rằng đối số truyền vào `defineProps()` giống hệt giá trị đưa vào option `props`: cùng một API option props được dùng chung cho cả hai kiểu khai báo.

</div>

<div class="options-api">

Props được khai báo bằng option [`props`](/api/options-state#props):

```js
export default {
  props: ['foo'],
  created() {
    // props được lộ ra trên `this`
    console.log(this.foo)
  }
}
```

</div>

Ngoài việc khai báo props bằng một mảng chuỗi, ta cũng có thể dùng cú pháp object:

<div class="options-api">

```js
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>
<div class="composition-api">

```js
// trong <script setup>
defineProps({
  title: String,
  likes: Number
})
```

```js
// trong component không dùng <script setup>
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>

Với mỗi property trong cú pháp khai báo bằng object, key là tên của prop còn value phải là hàm constructor của kiểu dữ liệu mong đợi.

Điều này không chỉ giúp tài liệu hóa component của bạn, mà còn cảnh báo cho các developer khác đang dùng component này trong console của trình duyệt nếu họ truyền sai kiểu. Chi tiết hơn về [kiểm tra tính hợp lệ của prop](#prop-validation) sẽ được nói ở phần dưới.

<div class="options-api">

Xem thêm: [Typing Component Props](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

Nếu bạn dùng TypeScript với `<script setup>`, bạn cũng có thể khai báo props bằng chú thích kiểu thuần túy:

```vue
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

Chi tiết hơn: [Typing Component Props](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

## Destructure Props Có Tính Phản Ứng <sup class="vt-badge" data-text="3.5+" /> \*\* {#reactive-props-destructure}

Hệ phản ứng của Vue theo dõi việc sử dụng state dựa trên truy cập property. Ví dụ, khi bạn truy cập `props.foo` trong getter của computed hoặc trong watcher, prop `foo` sẽ được theo dõi như một dependency.

Vì vậy, với đoạn mã sau:

```js
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // chỉ chạy một lần trước 3.5
  // chạy lại khi prop "foo" thay đổi ở 3.5+
  console.log(foo)
})
```

Ở phiên bản 3.4 trở xuống, `foo` là một hằng thực sự và sẽ không bao giờ thay đổi. Từ 3.5 trở lên, trình biên dịch của Vue sẽ tự động thêm tiền tố `props.` khi mã trong cùng block `<script setup>` truy cập biến được destructure từ `defineProps`. Vì vậy, đoạn mã trên tương đương với:

```js {5}
const props = defineProps(['foo'])

watchEffect(() => {
  // `foo` được trình biên dịch chuyển thành `props.foo`
  console.log(props.foo)
})
```

Ngoài ra, bạn có thể dùng cú pháp giá trị mặc định gốc của JavaScript để khai báo giá trị mặc định cho prop. Điều này đặc biệt hữu ích khi dùng cách khai báo props dựa trên kiểu:

```ts
const { foo = 'hello' } = defineProps<{ foo?: string }>()
```

Nếu muốn có sự phân biệt rõ ràng hơn giữa prop đã destructure và biến thường trong IDE, extension VSCode của Vue có một tùy chọn để bật inlay-hints cho prop đã destructure.

### Truyền Prop Đã Destructure Vào Hàm {#passing-destructured-props-into-functions}

Khi ta truyền một prop đã destructure vào một hàm, ví dụ:

```js
const { foo } = defineProps(['foo'])

watch(foo, /* ... */)
```

Điều này sẽ không hoạt động như mong đợi vì nó tương đương với `watch(props.foo, ...)` tức là ta đang truyền giá trị chứ không phải một nguồn dữ liệu phản ứng vào `watch`. Trên thực tế, trình biên dịch của Vue sẽ bắt các trường hợp như vậy và đưa ra cảnh báo.

Tương tự như cách ta có thể watch một prop bình thường bằng `watch(() => props.foo, ...)`, ta cũng có thể watch một prop đã destructure bằng cách bọc nó trong getter:

```js
watch(() => foo, /* ... */)
```

Ngoài ra, đây cũng là cách làm được khuyến nghị khi cần truyền một prop đã destructure vào hàm bên ngoài mà vẫn giữ được tính phản ứng:

```js
useComposable(() => foo)
```

Hàm bên ngoài có thể gọi getter đó (hoặc chuẩn hóa nó bằng [toValue](/api/reactivity-utilities.html#tovalue)) khi cần theo dõi sự thay đổi của prop được truyền vào, ví dụ trong getter của computed hoặc watcher.

</div>

## Chi Tiết Khi Truyền Prop {#prop-passing-details}

### Kiểu Chữ Của Tên Prop {#prop-name-casing}

Ta khai báo tên prop dài dưới dạng camelCase vì làm như vậy sẽ tránh phải đặt chúng trong dấu nháy khi dùng làm key của object, đồng thời cho phép tham chiếu trực tiếp trong biểu thức template vì chúng là định danh JavaScript hợp lệ:

<div class="composition-api">

```js
defineProps({
  greetingMessage: String
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    greetingMessage: String
  }
}
```

</div>

```vue-html
<span>{{ greetingMessage }}</span>
```

Về mặt kỹ thuật, bạn cũng có thể dùng camelCase khi truyền prop xuống component con (ngoại trừ trong [template viết trực tiếp trong DOM](/guide/essentials/component-basics#in-dom-template-parsing-caveats)). Tuy nhiên, quy ước chung là dùng kebab-case trong mọi trường hợp để nhất quán với thuộc tính HTML:

```vue-html
<MyComponent greeting-message="hello" />
```

Ta dùng [PascalCase cho thẻ component](/guide/components/registration#component-name-casing) khi có thể vì nó giúp template dễ đọc hơn bằng cách phân biệt component Vue với phần tử gốc. Tuy nhiên, dùng camelCase khi truyền prop không mang lại lợi ích thực tế tương đương, nên ta chọn đi theo quy ước của từng ngôn ngữ.

### Prop Tĩnh Và Prop Động {#static-vs-dynamic-props}

Cho đến lúc này, bạn đã thấy prop được truyền vào dưới dạng giá trị tĩnh, ví dụ:

```vue-html
<BlogPost title="My journey with Vue" />
```

Bạn cũng đã thấy prop được gán động bằng `v-bind` hoặc cú pháp rút gọn `:`, ví dụ:

```vue-html
<!-- Gán động giá trị của một biến -->
<BlogPost :title="post.title" />

<!-- Gán động giá trị của một biểu thức phức tạp -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

### Truyền Nhiều Kiểu Giá Trị Khác Nhau {#passing-different-value-types}

Trong hai ví dụ trên, ta tình cờ truyền giá trị chuỗi, nhưng prop có thể nhận _bất kỳ_ kiểu giá trị nào.

#### Number {#number}

```vue-html
<!-- Dù `42` là tĩnh, ta vẫn cần v-bind để nói với Vue rằng -->
<!-- đây là một biểu thức JavaScript chứ không phải chuỗi.    -->
<BlogPost :likes="42" />

<!-- Gán động giá trị của một biến. -->
<BlogPost :likes="post.likes" />
```

#### Boolean {#boolean}

```vue-html
<!-- Chỉ cần xuất hiện prop mà không có giá trị sẽ ngầm hiểu là `true`. -->
<BlogPost is-published />

<!-- Dù `false` là tĩnh, ta vẫn cần v-bind để nói với Vue rằng -->
<!-- đây là một biểu thức JavaScript chứ không phải chuỗi.      -->
<BlogPost :is-published="false" />

<!-- Gán động giá trị của một biến. -->
<BlogPost :is-published="post.isPublished" />
```

#### Array {#array}

```vue-html
<!-- Dù mảng là tĩnh, ta vẫn cần v-bind để nói với Vue rằng -->
<!-- đây là một biểu thức JavaScript chứ không phải chuỗi.  -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- Gán động giá trị của một biến. -->
<BlogPost :comment-ids="post.commentIds" />
```

#### Object {#object}

```vue-html
<!-- Dù object là tĩnh, ta vẫn cần v-bind để nói với Vue rằng -->
<!-- đây là một biểu thức JavaScript chứ không phải chuỗi.    -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- Gán động giá trị của một biến. -->
<BlogPost :author="post.author" />
```

### Bind Nhiều Property Bằng Một Object {#binding-multiple-properties-using-an-object}

Nếu muốn truyền toàn bộ property của một object làm props, bạn có thể dùng [`v-bind` không có đối số](/guide/essentials/template-syntax#dynamically-binding-multiple-attributes) (`v-bind` thay vì `:prop-name`). Ví dụ, với một object `post`:

<div class="options-api">

```js
export default {
  data() {
    return {
      post: {
        id: 1,
        title: 'My Journey with Vue'
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

</div>

Template sau:

```vue-html
<BlogPost v-bind="post" />
```

Sẽ tương đương với:

```vue-html
<BlogPost :id="post.id" :title="post.title" />
```

## Luồng Dữ Liệu Một Chiều {#one-way-data-flow}

Mọi prop đều tạo thành một **liên kết một chiều đi xuống** giữa property ở component con và property ở component cha: khi property ở component cha cập nhật, nó sẽ truyền xuống component con, nhưng không đi theo chiều ngược lại. Điều này ngăn component con vô tình thay đổi state của component cha, vốn có thể khiến luồng dữ liệu của ứng dụng khó hiểu hơn.

Ngoài ra, mỗi lần component cha cập nhật, tất cả prop trong component con cũng sẽ được làm mới bằng giá trị mới nhất. Điều này có nghĩa là bạn **không nên** cố thay đổi prop bên trong component con. Nếu làm vậy, Vue sẽ cảnh báo trong console:

<div class="composition-api">

```js
const props = defineProps(['foo'])

// ❌ cảnh báo, props là readonly!
props.foo = 'bar'
```

</div>
<div class="options-api">

```js
export default {
  props: ['foo'],
  created() {
    // ❌ cảnh báo, props là readonly!
    this.foo = 'bar'
  }
}
```

</div>

Thông thường, có hai trường hợp dễ khiến ta muốn thay đổi prop:

1. **Prop được dùng để truyền một giá trị ban đầu; sau đó component con muốn dùng nó như một property dữ liệu cục bộ.** Trong trường hợp này, tốt nhất hãy định nghĩa một property dữ liệu cục bộ dùng prop làm giá trị khởi tạo:

   <div class="composition-api">

   ```js
   const props = defineProps(['initialCounter'])

   // counter chỉ dùng props.initialCounter làm giá trị ban đầu;
   // nó sẽ tách khỏi các lần cập nhật prop về sau.
   const counter = ref(props.initialCounter)
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['initialCounter'],
     data() {
       return {
         // counter chỉ dùng this.initialCounter làm giá trị ban đầu;
         // nó sẽ tách khỏi các lần cập nhật prop về sau.
         counter: this.initialCounter
       }
     }
   }
   ```

   </div>

2. **Prop được truyền vào như một giá trị thô cần được biến đổi.** Trong trường hợp này, tốt nhất nên định nghĩa một computed dùng giá trị của prop:

   <div class="composition-api">

   ```js
   const props = defineProps(['size'])

   // thuộc tính computed sẽ tự cập nhật khi prop thay đổi
   const normalizedSize = computed(() => props.size.trim().toLowerCase())
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['size'],
     computed: {
       // thuộc tính computed sẽ tự cập nhật khi prop thay đổi
       normalizedSize() {
         return this.size.trim().toLowerCase()
       }
     }
   }
   ```

   </div>

### Thay Đổi Prop Kiểu Object / Array {#mutating-object-array-props}

Khi object và array được truyền làm prop, dù component con không thể thay đổi bản thân liên kết của prop, nó **vẫn có thể** thay đổi các property lồng bên trong object hoặc array đó. Lý do là vì trong JavaScript, object và array được truyền bằng tham chiếu, và Vue sẽ phải trả một chi phí quá lớn nếu cố ngăn chặn hoàn toàn các thay đổi kiểu này.

Nhược điểm chính của kiểu thay đổi đó là nó cho phép component con ảnh hưởng tới state của component cha theo một cách không rõ ràng với component cha, khiến việc suy luận về luồng dữ liệu sau này có thể khó hơn. Best practice là nên tránh những thay đổi như vậy trừ khi component cha và component con vốn đã được thiết kế để gắn chặt với nhau. Trong đa số trường hợp, component con nên [phát ra một sự kiện](/guide/components/events) để component cha tự thực hiện thay đổi.

## Kiểm Tra Tính Hợp Lệ Của Prop {#prop-validation}

Component có thể chỉ định yêu cầu cho prop, chẳng hạn như kiểu dữ liệu như bạn đã thấy ở trên. Nếu không đáp ứng yêu cầu, Vue sẽ cảnh báo trong console JavaScript của trình duyệt. Điều này đặc biệt hữu ích khi bạn phát triển một component để người khác dùng.

Để chỉ định validation cho prop, thay vì truyền một mảng chuỗi, bạn có thể truyền vào <span class="composition-api">macro `defineProps()`</span><span class="options-api">option `props`</span> một object chứa các yêu cầu validation. Ví dụ:

<div class="composition-api">

```js
defineProps({
  // Kiểm tra kiểu cơ bản
  // (`null` và `undefined` sẽ cho phép mọi kiểu)
  propA: Number,
  // Nhiều kiểu có thể chấp nhận
  propB: [String, Number],
  // Chuỗi bắt buộc
  propC: {
    type: String,
    required: true
  },
  // Chuỗi bắt buộc nhưng có thể null
  propD: {
    type: [String, null],
    required: true
  },
  // Number có giá trị mặc định
  propE: {
    type: Number,
    default: 100
  },
  // Object có giá trị mặc định
  propF: {
    type: Object,
    // Giá trị mặc định cho object hoặc array phải được trả về từ
    // một factory function. Hàm này nhận raw props
    // mà component nhận được làm đối số.
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // Hàm validator tùy chỉnh
  // full props được truyền làm đối số thứ 2 từ 3.4+
  propG: {
    validator(value, props) {
      // Giá trị phải khớp với một trong các chuỗi này
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // Function có giá trị mặc định
  propH: {
    type: Function,
    // Khác với default của object hoặc array, đây không phải factory
    // function, mà là hàm đóng vai trò giá trị mặc định
    default() {
      return 'Default function'
    }
  }
})
```

:::tip
Mã bên trong đối số của `defineProps()` **không thể truy cập các biến khác được khai báo trong `<script setup>`**, vì toàn bộ biểu thức này sẽ được chuyển ra scope của một hàm bên ngoài khi biên dịch.
:::

</div>
<div class="options-api">

```js
export default {
  props: {
    // Kiểm tra kiểu cơ bản
    // (`null` và `undefined` sẽ cho phép mọi kiểu)
    propA: Number,
    // Nhiều kiểu có thể chấp nhận
    propB: [String, Number],
    // Chuỗi bắt buộc
    propC: {
      type: String,
      required: true
    },
    // Chuỗi bắt buộc nhưng có thể null
    propD: {
      type: [String, null],
      required: true
    },
    // Number có giá trị mặc định
    propE: {
      type: Number,
      default: 100
    },
    // Object có giá trị mặc định
    propF: {
      type: Object,
      // Giá trị mặc định cho object hoặc array phải được trả về từ
      // một factory function. Hàm này nhận raw props
      // mà component nhận được làm đối số.
      default(rawProps) {
        return { message: 'hello' }
      }
    },
    // Hàm validator tùy chỉnh
    // full props được truyền làm đối số thứ 2 từ 3.4+
    propG: {
      validator(value, props) {
        // Giá trị phải khớp với một trong các chuỗi này
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // Function có giá trị mặc định
    propH: {
      type: Function,
      // Khác với default của object hoặc array, đây không phải factory
      // function, mà là hàm đóng vai trò giá trị mặc định
      default() {
        return 'Default function'
      }
    }
  }
}
```

</div>

Chi tiết bổ sung:

- Mặc định mọi prop đều là tùy chọn, trừ khi có `required: true`.

- Prop tùy chọn nhưng vắng mặt, nếu không phải `Boolean`, sẽ có giá trị `undefined`.

- Prop `Boolean` vắng mặt sẽ bị ép thành `false`. Bạn có thể thay đổi hành vi này bằng cách đặt `default` cho nó, ví dụ `default: undefined` để nó hoạt động giống prop không phải Boolean.

- Nếu có chỉ định giá trị `default`, nó sẽ được dùng khi giá trị prop sau khi resolve là `undefined`, bao gồm cả khi prop vắng mặt hoặc khi ta truyền tường minh giá trị `undefined`.

Khi validation prop thất bại, Vue sẽ phát ra cảnh báo trong console (nếu dùng bản build cho môi trường phát triển).

<div class="composition-api">

Nếu dùng [cách khai báo props dựa trên kiểu](/api/sfc-script-setup#type-only-props-emit-declarations) <sup class="vt-badge ts" />, Vue sẽ cố gắng biên dịch chú thích kiểu thành khai báo prop runtime tương đương. Ví dụ, `defineProps<{ msg: string }>` sẽ được biên dịch thành `{ msg: { type: String, required: true }}`.

</div>
<div class="options-api">

::: tip Lưu ý
Hãy nhớ rằng props được validate **trước khi** instance component được tạo ra, vì vậy các property của instance (ví dụ `data`, `computed`, v.v.) sẽ không khả dụng bên trong hàm `default` hay `validator`.
:::

</div>

### Kiểm Tra Kiểu Lúc Chạy {#runtime-type-checks}

`type` có thể là một trong những constructor gốc sau:

- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`
- `Error`

Ngoài ra, `type` cũng có thể là một class tùy chỉnh hoặc một hàm constructor tùy chỉnh, và phép kiểm tra sẽ được thực hiện bằng `instanceof`. Ví dụ, với class sau:

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

Bạn có thể dùng nó làm kiểu cho prop:

<div class="composition-api">

```js
defineProps({
  author: Person
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    author: Person
  }
}
```

</div>

Vue sẽ dùng `instanceof Person` để kiểm tra xem giá trị của prop `author` có thực sự là một instance của class `Person` hay không.

### Kiểu Có Thể Null {#nullable-type}

Nếu kiểu là bắt buộc nhưng có thể null, bạn có thể dùng cú pháp mảng có chứa `null`:

<div class="composition-api">

```js
defineProps({
  id: {
    type: [String, null],
    required: true
  }
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    id: {
      type: [String, null],
      required: true
    }
  }
}
```

</div>

Lưu ý rằng nếu `type` chỉ đơn thuần là `null` mà không dùng cú pháp mảng, nó sẽ chấp nhận mọi kiểu.

## Ép Kiểu Boolean {#boolean-casting}

Prop có kiểu `Boolean` có quy tắc ép kiểu đặc biệt để bắt chước hành vi của thuộc tính boolean trong HTML gốc. Với `<MyComponent>` có khai báo như sau:

<div class="composition-api">

```js
defineProps({
  disabled: Boolean
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    disabled: Boolean
  }
}
```

</div>

Component này có thể được dùng như sau:

```vue-html
<!-- tương đương với truyền :disabled="true" -->
<MyComponent disabled />

<!-- tương đương với truyền :disabled="false" -->
<MyComponent />
```

Khi một prop được khai báo cho phép nhiều kiểu, quy tắc ép kiểu của `Boolean` cũng sẽ được áp dụng. Tuy nhiên có một trường hợp cần chú ý là khi vừa cho phép `String` vừa cho phép `Boolean`: quy tắc ép kiểu của Boolean chỉ áp dụng nếu `Boolean` đứng trước `String`:

<div class="composition-api">

```js
// disabled sẽ bị ép thành true
defineProps({
  disabled: [Boolean, Number]
})

// disabled sẽ bị ép thành true
defineProps({
  disabled: [Boolean, String]
})

// disabled sẽ bị ép thành true
defineProps({
  disabled: [Number, Boolean]
})

// disabled sẽ được phân tích thành chuỗi rỗng (disabled="")
defineProps({
  disabled: [String, Boolean]
})
```

</div>
<div class="options-api">

```js
// disabled sẽ bị ép thành true
export default {
  props: {
    disabled: [Boolean, Number]
  }
}

// disabled sẽ bị ép thành true
export default {
  props: {
    disabled: [Boolean, String]
  }
}

// disabled sẽ bị ép thành true
export default {
  props: {
    disabled: [Number, Boolean]
  }
}

// disabled sẽ được phân tích thành chuỗi rỗng (disabled="")
export default {
  props: {
    disabled: [String, Boolean]
  }
}
```

</div>
