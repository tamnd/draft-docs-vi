---
outline: deep
---

# Nền Tảng về Tính Phản Ứng {#reactivity-fundamentals}

:::tip Tuỳ chọn API
Trang này và nhiều chương khác trong hướng dẫn có nội dung khác nhau tuỳ theo Options API hay Composition API. Tuỳ chọn hiện tại của bạn là <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Bạn có thể chuyển đổi giữa hai kiểu API bằng nút "API Preference" ở đầu thanh bên trái.
:::

<div class="options-api">

## Khai Báo State Phản Ứng \* {#declaring-reactive-state}

Với Options API, chúng ta dùng tuỳ chọn `data` để khai báo state phản ứng của một component. Giá trị của tuỳ chọn này phải là một hàm trả về một object. Vue sẽ gọi hàm đó khi tạo một instance component mới, và bọc object trả về vào trong hệ thống tính phản ứng của nó. Tất cả các thuộc tính cấp cao nhất của object này sẽ được proxy lên instance component (tức là `this` trong các method và lifecycle hook):

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` is a lifecycle hook which we will explain later
  mounted() {
    // `this` refers to the component instance.
    console.log(this.count) // => 1

    // data can be mutated as well
    this.count = 2
  }
}
```

[Thử trong Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Các thuộc tính instance này chỉ được thêm vào khi instance được tạo lần đầu, vì vậy bạn cần đảm bảo rằng tất cả chúng đều có mặt trong object mà hàm `data` trả về. Nếu cần, hãy dùng `null`, `undefined` hoặc giá trị giữ chỗ khác cho những thuộc tính mà giá trị thực chưa có sẵn.

Bạn có thể thêm một thuộc tính mới trực tiếp vào `this` mà không khai báo trong `data`. Tuy nhiên, những thuộc tính được thêm theo cách này sẽ không thể kích hoạt các cập nhật phản ứng.

Vue dùng tiền tố `$` khi hiển thị các API tích hợp sẵn của nó qua instance component. Nó cũng dùng tiền tố `_` cho các thuộc tính nội bộ. Bạn nên tránh đặt tên thuộc tính cấp cao nhất trong `data` bắt đầu bằng một trong hai ký tự này.

### Reactive Proxy và Object Gốc \* {#reactive-proxy-vs-original}

Trong Vue 3, dữ liệu được làm cho phản ứng bằng cách sử dụng [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Người dùng từ Vue 2 cần lưu ý trường hợp ngoại lệ sau:

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

Khi bạn truy cập `this.someObject` sau khi gán, giá trị nhận được là một reactive proxy của `newObject` gốc. **Khác với Vue 2, `newObject` gốc vẫn giữ nguyên và không bị biến thành phản ứng: hãy luôn truy cập state phản ứng thông qua thuộc tính của `this`.**

</div>

<div class="composition-api">

## Khai Báo State Phản Ứng \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

Trong Composition API, cách được khuyến nghị để khai báo state phản ứng là dùng hàm [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` nhận vào một đối số và trả về nó được bọc trong một ref object có thuộc tính `.value`:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Xem thêm: [Typing Refs](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Để truy cập ref trong template của một component, hãy khai báo và trả về chúng từ hàm `setup()` của component đó:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` is a special hook dedicated for the Composition API.
  setup() {
    const count = ref(0)

    // expose the ref to the template
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Lưu ý rằng chúng ta **không** cần thêm `.value` khi dùng ref trong template. Để thuận tiện, ref sẽ được tự động mở bọc khi sử dụng bên trong template (với một vài [lưu ý](#caveat-when-unwrapping-in-templates)).

Bạn cũng có thể thay đổi giá trị ref trực tiếp trong các event handler:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Với logic phức tạp hơn, chúng ta có thể khai báo các hàm thay đổi ref trong cùng phạm vi và expose chúng như các method cùng với state:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value is needed in JavaScript
      count.value++
    }

    // don't forget to expose the function as well.
    return {
      count,
      increment
    }
  }
}
```

Các method được expose có thể dùng làm event handler:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Đây là ví dụ chạy trực tiếp trên [Codepen](https://codepen.io/vuejs-examples/pen/WNYbaqo), không cần công cụ build nào.

### `<script setup>` \*\* {#script-setup}

Việc expose state và method thủ công qua `setup()` có thể khá dài dòng. May mắn thay, điều này có thể tránh được khi dùng [Single-File Components (SFC)](/guide/scaling-up/sfc). Chúng ta có thể rút gọn bằng cách dùng `<script setup>`:

```vue{1}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

Các import, biến và hàm được khai báo ở cấp cao nhất trong `<script setup>` đều có thể dùng trực tiếp trong template của component đó. Hãy nghĩ template như một hàm JavaScript được khai báo trong cùng phạm vi — nó có thể truy cập mọi thứ được khai báo bên cạnh nó một cách tự nhiên.

:::tip
Trong phần còn lại của hướng dẫn, chúng tôi chủ yếu dùng cú pháp SFC + `<script setup>` cho các ví dụ code Composition API, vì đây là cách sử dụng phổ biến nhất đối với các lập trình viên Vue.

Nếu bạn không dùng SFC, bạn vẫn có thể dùng Composition API với tuỳ chọn [`setup()`](/api/composition-api-setup).
:::

### Tại Sao Cần Ref? \*\* {#why-refs}

Bạn có thể thắc mắc tại sao chúng ta cần ref với `.value` thay vì chỉ dùng biến thông thường. Để giải thích điều này, chúng ta cần nói qua về cách hệ thống tính phản ứng của Vue hoạt động.

Khi bạn dùng ref trong template và thay đổi giá trị của nó sau đó, Vue tự động phát hiện thay đổi và cập nhật DOM tương ứng. Điều này được thực hiện nhờ hệ thống tính phản ứng theo dõi dependency. Khi một component được render lần đầu, Vue **theo dõi** mọi ref được dùng trong quá trình render đó. Sau này, khi một ref bị thay đổi, nó sẽ **kích hoạt** quá trình re-render cho các component đang theo dõi nó.

Trong JavaScript thông thường, không có cách nào để phát hiện việc truy cập hay thay đổi giá trị của biến thông thường. Tuy nhiên, chúng ta có thể chặn các thao tác get và set trên thuộc tính của object thông qua getter và setter.

Thuộc tính `.value` cho Vue cơ hội phát hiện khi nào một ref được truy cập hay thay đổi. Vue thực hiện việc theo dõi trong getter và kích hoạt trong setter. Về mặt khái niệm, bạn có thể hình dung ref như một object trông như thế này:

```js
// pseudo code, not actual implementation
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

Một ưu điểm nữa của ref là khác với biến thông thường, bạn có thể truyền ref vào hàm mà vẫn giữ được quyền truy cập giá trị mới nhất và kết nối phản ứng. Điều này đặc biệt hữu ích khi tái cấu trúc logic phức tạp thành code tái sử dụng được.

Hệ thống tính phản ứng được thảo luận chi tiết hơn trong phần [Reactivity in Depth](/guide/extras/reactivity-in-depth).
</div>

<div class="options-api">

## Khai Báo Method \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Free Vue.js Methods Lesson"/>

Để thêm method vào instance component, chúng ta dùng tuỳ chọn `methods`. Đây phải là một object chứa các method mong muốn:

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // methods can be called in lifecycle hooks, or other methods!
    this.increment()
  }
}
```

Vue tự động bind giá trị `this` cho `methods` để nó luôn tham chiếu đến instance component. Điều này đảm bảo method giữ đúng giá trị `this` nếu được dùng làm event listener hoặc callback. Bạn nên tránh dùng arrow function khi định nghĩa `methods`, vì điều đó ngăn Vue bind giá trị `this` thích hợp:

```js
export default {
  methods: {
    increment: () => {
      // BAD: no `this` access here!
    }
  }
}
```

Cũng như tất cả các thuộc tính khác của instance component, `methods` có thể truy cập từ bên trong template của component. Trong template, chúng thường được dùng nhất làm event listener:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Thử trong Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

Trong ví dụ trên, method `increment` sẽ được gọi khi `<button>` được nhấp.

</div>

### Tính Phản Ứng Sâu {#deep-reactivity}

<div class="options-api">

Trong Vue, state mặc định có tính phản ứng sâu. Điều này có nghĩa là bạn có thể mong đợi các thay đổi được phát hiện ngay cả khi bạn thay đổi các object hoặc mảng lồng nhau:

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // these will work as expected.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ref có thể chứa bất kỳ kiểu giá trị nào, bao gồm cả object lồng nhau, mảng, hoặc các cấu trúc dữ liệu tích hợp sẵn của JavaScript như `Map`.

Một ref sẽ làm cho giá trị bên trong của nó có tính phản ứng sâu. Điều này có nghĩa là bạn có thể mong đợi các thay đổi được phát hiện ngay cả khi bạn thay đổi các object hoặc mảng lồng nhau:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // these will work as expected.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Các giá trị không phải kiểu nguyên thủy sẽ được chuyển đổi thành reactive proxy thông qua [`reactive()`](#reactive), được thảo luận bên dưới.

Bạn cũng có thể từ chối tính phản ứng sâu bằng cách dùng [shallow refs](/api/reactivity-advanced#shallowref). Với shallow ref, chỉ việc truy cập `.value` mới được theo dõi cho tính phản ứng. Shallow ref có thể dùng để tối ưu hiệu năng bằng cách tránh chi phí quan sát các object lớn, hoặc trong trường hợp state bên trong được quản lý bởi một thư viện bên ngoài.

Đọc thêm:

- [Reduce Reactivity Overhead for Large Immutable Structures](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Integration with External State Systems](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Thời Điểm Cập Nhật DOM {#dom-update-timing}

Khi bạn thay đổi state phản ứng, DOM sẽ được cập nhật tự động. Tuy nhiên, cần lưu ý rằng các cập nhật DOM không được áp dụng ngay lập tức. Thay vào đó, Vue đệm chúng lại cho đến "next tick" trong chu kỳ cập nhật để đảm bảo mỗi component chỉ cập nhật một lần dù bạn đã thực hiện bao nhiêu thay đổi state.

Để chờ cập nhật DOM hoàn thành sau khi thay đổi state, bạn có thể dùng API toàn cục [nextTick()](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Now the DOM is updated
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    async increment() {
      this.count++
      await nextTick()
      // Now the DOM is updated
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Có một cách khác để khai báo state phản ứng, đó là dùng API `reactive()`. Khác với ref — bọc giá trị bên trong vào một object đặc biệt — `reactive()` làm cho chính object đó trở nên phản ứng:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> Xem thêm: [Typing Reactive](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

Cách dùng trong template:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Các reactive object là [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) và hoạt động giống như các object thông thường. Điểm khác biệt là Vue có thể chặn việc truy cập và thay đổi tất cả các thuộc tính của một reactive object để theo dõi và kích hoạt tính phản ứng.

`reactive()` chuyển đổi object một cách sâu: các object lồng nhau cũng được bọc trong `reactive()` khi được truy cập. Nó cũng được `ref()` gọi nội bộ khi giá trị ref là một object. Tương tự với shallow ref, cũng có API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) để từ chối tính phản ứng sâu.

### Reactive Proxy và Object Gốc \*\* {#reactive-proxy-vs-original-1}

Điều quan trọng cần lưu ý là giá trị trả về từ `reactive()` là một [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) của object gốc, không bằng object gốc:

```js
const raw = {}
const proxy = reactive(raw)

// proxy is NOT equal to the original.
console.log(proxy === raw) // false
```

Chỉ có proxy mới có tính phản ứng — việc thay đổi object gốc sẽ không kích hoạt cập nhật. Vì vậy, cách thực hành tốt nhất khi làm việc với hệ thống tính phản ứng của Vue là **chỉ dùng phiên bản được proxy của state**.

Để đảm bảo truy cập proxy nhất quán, việc gọi `reactive()` trên cùng một object sẽ luôn trả về cùng một proxy, và gọi `reactive()` trên một proxy đã có cũng trả về chính proxy đó:

```js
// calling reactive() on the same object returns the same proxy
console.log(reactive(raw) === proxy) // true

// calling reactive() on a proxy returns itself
console.log(reactive(proxy) === proxy) // true
```

Quy tắc này cũng áp dụng cho các object lồng nhau. Do tính phản ứng sâu, các object lồng nhau bên trong một reactive object cũng là proxy:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Hạn Chế của `reactive()` \*\* {#limitations-of-reactive}

API `reactive()` có một số hạn chế:

1. **Kiểu giá trị bị giới hạn:** nó chỉ hoạt động với kiểu object (object, mảng, và [các kiểu collection](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) như `Map` và `Set`). Nó không thể chứa [các kiểu nguyên thủy](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) như `string`, `number` hay `boolean`.

2. **Không thể thay thế toàn bộ object:** vì cơ chế theo dõi tính phản ứng của Vue hoạt động dựa trên truy cập thuộc tính, chúng ta phải luôn giữ nguyên tham chiếu đến reactive object. Điều này có nghĩa là chúng ta không thể dễ dàng "thay thế" một reactive object vì kết nối phản ứng đến tham chiếu đầu tiên sẽ bị mất:

   ```js
   let state = reactive({ count: 0 })

   // the above reference ({ count: 0 }) is no longer being tracked
   // (reactivity connection is lost!)
   state = reactive({ count: 1 })
   ```

3. **Không thân thiện với destructuring:** khi chúng ta destructure thuộc tính kiểu nguyên thủy của một reactive object thành biến cục bộ, hoặc khi chúng ta truyền thuộc tính đó vào hàm, chúng ta sẽ mất kết nối phản ứng:

   ```js
   const state = reactive({ count: 0 })

   // count is disconnected from state.count when destructured.
   let { count } = state
   // does not affect original state
   count++

   // the function receives a plain number and
   // won't be able to track changes to state.count
   // we have to pass the entire object in to retain reactivity
   callSomeFunction(state.count)
   ```

Do những hạn chế này, chúng tôi khuyến nghị dùng `ref()` là API chính để khai báo state phản ứng.

## Chi Tiết Thêm về Mở Bọc Ref \*\* {#additional-ref-unwrapping-details}

### Là Thuộc Tính của Reactive Object \*\* {#ref-unwrapping-as-reactive-object-property}

Một ref sẽ được tự động mở bọc khi được truy cập hoặc thay đổi như một thuộc tính của reactive object. Nói cách khác, nó hoạt động như một thuộc tính thông thường:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Nếu một ref mới được gán cho thuộc tính đang liên kết với một ref cũ, nó sẽ thay thế ref cũ đó:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// original ref is now disconnected from state.count
console.log(count.value) // 1
```

Việc mở bọc ref chỉ xảy ra khi được lồng bên trong một reactive object sâu. Nó không áp dụng khi được truy cập như thuộc tính của một [shallow reactive object](/api/reactivity-advanced#shallowreactive).

### Lưu Ý với Mảng và Collection \*\* {#caveat-in-arrays-and-collections}

Khác với reactive object, **không** có mở bọc nào được thực hiện khi ref được truy cập như một phần tử của reactive array hoặc kiểu collection gốc như `Map`:

```js
const books = reactive([ref('Vue 3 Guide')])
// need .value here
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// need .value here
console.log(map.get('count').value)
```

### Lưu Ý Khi Mở Bọc trong Template \*\* {#caveat-when-unwrapping-in-templates}

Việc mở bọc ref trong template chỉ áp dụng nếu ref là thuộc tính cấp cao nhất trong ngữ cảnh render của template.

Trong ví dụ dưới đây, `count` và `object` là thuộc tính cấp cao nhất, nhưng `object.id` thì không:

```js
const count = ref(0)
const object = { id: ref(1) }
```

Vì vậy, biểu thức này hoạt động đúng như mong đợi:

```vue-html
{{ count + 1 }}
```

...trong khi biểu thức này thì **KHÔNG**:

```vue-html
{{ object.id + 1 }}
```

Kết quả render sẽ là `[object Object]1` vì `object.id` không được mở bọc khi đánh giá biểu thức và vẫn là một ref object. Để khắc phục, chúng ta có thể destructure `id` thành thuộc tính cấp cao nhất:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Bây giờ kết quả render sẽ là `2`.

Một điều cần lưu ý nữa là ref sẽ được mở bọc nếu nó là giá trị cuối cùng được đánh giá trong một phép nội suy văn bản (tức là thẻ <code v-pre>{{ }}</code>), vì vậy biểu thức sau sẽ render ra `1`:

```vue-html
{{ object.id }}
```

Đây chỉ là tính năng tiện lợi của phép nội suy văn bản và tương đương với <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Method Có State \* {#stateful-methods}

Trong một số trường hợp, chúng ta có thể cần tạo động một hàm method, ví dụ như tạo một event handler được debounce:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Debouncing with Lodash
    click: debounce(function () {
      // ... respond to click ...
    }, 500)
  }
}
```

Tuy nhiên, cách này có vấn đề với các component được tái sử dụng vì hàm debounce là **có state**: nó duy trì một số state nội bộ về thời gian đã trôi qua. Nếu nhiều instance component chia sẻ cùng một hàm debounce, chúng sẽ can thiệp lẫn nhau.

Để giữ cho hàm debounce của mỗi instance component độc lập, chúng ta có thể tạo phiên bản debounce trong lifecycle hook `created`:

```js
export default {
  created() {
    // each instance now has its own copy of debounced handler
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // also a good idea to cancel the timer
    // when the component is removed
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... respond to click ...
    }
  }
}
```

</div>
