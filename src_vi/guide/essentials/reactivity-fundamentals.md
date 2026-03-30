---
outline: deep
---

# Nền tảng về tính phản ứng {#reactivity-fundamentals}

:::tip Tùy chọn API
Trang này và nhiều chương sau trong hướng dẫn có nội dung khác nhau cho Options API và Composition API. Tùy chọn hiện tại của bạn là <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Bạn có thể chuyển qua lại giữa hai kiểu API bằng công tắc "API Preference" ở đầu thanh bên trái.
:::

<div class="options-api">

## Khai báo state phản ứng \* {#declaring-reactive-state}

Với Options API, chúng ta dùng option `data` để khai báo state phản ứng của component. Giá trị của option này phải là một hàm trả về object. Vue sẽ gọi hàm đó khi tạo một component instance mới, rồi đưa object trả về vào hệ thống tính phản ứng của nó. Mọi property ở cấp cao nhất của object này sẽ được proxy lên component instance, tức là `this` trong method và hook vòng đời:

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` là một hook vòng đời, chúng ta sẽ giải thích sau
  mounted() {
    // `this` trỏ tới component instance.
    console.log(this.count) // => 1

    // data cũng có thể bị thay đổi
    this.count = 2
  }
}
```

[Thử trên Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Những property này chỉ được thêm vào instance khi instance được tạo lần đầu, nên bạn cần đảm bảo tất cả chúng đều có mặt trong object do hàm `data` trả về. Khi cần, hãy dùng `null`, `undefined` hoặc một giá trị tạm khác cho những property mà giá trị mong muốn vẫn chưa có sẵn.

Cũng có thể thêm một property mới trực tiếp vào `this` mà không khai báo trong `data`. Tuy nhiên, property được thêm theo cách này sẽ không kích hoạt cập nhật phản ứng.

Vue dùng tiền tố `$` khi đưa các API dựng sẵn của nó ra thông qua component instance. Nó cũng dành riêng tiền tố `_` cho các property nội bộ. Bạn nên tránh đặt tên property cấp cao nhất trong `data` bắt đầu bằng một trong hai ký tự này.

### Proxy phản ứng và object gốc \* {#reactive-proxy-vs-original}

Trong Vue 3, dữ liệu được đưa vào hệ thống tính phản ứng bằng [JavaScript Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Nếu bạn chuyển từ Vue 2 sang thì nên để ý trường hợp đặc biệt sau:

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

Khi bạn truy cập `this.someObject` sau khi gán, giá trị nhận được là một proxy phản ứng của `newObject` gốc. **Khác với Vue 2, `newObject` gốc vẫn được giữ nguyên và không trở thành phản ứng: hãy luôn nhớ truy cập state phản ứng thông qua property của `this`.**

</div>

<div class="composition-api">

## Khai báo state phản ứng \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

Trong Composition API, cách được khuyến nghị để khai báo state phản ứng là dùng hàm [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` nhận một đối số rồi trả về nó trong một object ref có property `.value`:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Xem thêm: [Khai báo kiểu cho ref](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Để truy cập ref trong template của component, hãy khai báo và trả chúng về từ hàm `setup()` của component:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` là một hook đặc biệt dành riêng cho Composition API.
  setup() {
    const count = ref(0)

    // đưa ref ra để template dùng được
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Hãy để ý rằng chúng ta **không** cần thêm `.value` khi dùng ref trong template. Để tiện hơn, ref sẽ được tự động mở bọc khi dùng trong template, dù vẫn có một vài [điểm cần lưu ý](#caveat-when-unwrapping-in-templates).

Bạn cũng có thể thay đổi ref trực tiếp trong event handler:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Với logic phức tạp hơn, ta có thể khai báo những hàm thay đổi ref trong cùng phạm vi và đưa chúng ra như method bên cạnh state:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // trong JavaScript thì cần dùng .value
      count.value++
    }

    // đừng quên đưa luôn hàm này ra ngoài.
    return {
      count,
      increment
    }
  }
}
```

Những method được đưa ra ngoài sau đó có thể dùng làm event handler:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Dưới đây là ví dụ chạy trực tiếp trên [Codepen](https://codepen.io/vuejs-examples/pen/WNYbaqo), không dùng build tool nào.

### `<script setup>` \*\* {#script-setup}

Việc tự tay đưa state và method ra ngoài thông qua `setup()` đôi khi khá dài dòng. May mắn là điều này có thể tránh được khi dùng [Single-File Component (SFC)](/guide/scaling-up/sfc). Ta có thể viết gọn hơn với `<script setup>`:

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

[Thử trên Playground](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

Những import, biến và hàm ở cấp cao nhất được khai báo trong `<script setup>` sẽ tự động dùng được trong template của cùng component đó. Hãy xem template như một hàm JavaScript được khai báo trong cùng phạm vi, nên nó tự nhiên có quyền truy cập vào mọi thứ được khai báo bên cạnh nó.

:::tip
Trong phần còn lại của hướng dẫn, chúng tôi sẽ chủ yếu dùng cú pháp SFC + `<script setup>` cho các ví dụ Composition API, vì đó là cách dùng phổ biến nhất với lập trình viên Vue.

Nếu bạn không dùng SFC, bạn vẫn có thể dùng Composition API thông qua option [`setup()`](/api/composition-api-setup).
:::

### Tại sao dùng Ref? \*\* {#why-refs}

Có thể bạn sẽ thắc mắc vì sao phải dùng ref với `.value` thay vì chỉ dùng biến thông thường. Để giải thích điều này, chúng ta cần nói ngắn gọn về cách hệ thống tính phản ứng của Vue hoạt động.

Khi bạn dùng ref trong template rồi thay đổi giá trị của nó về sau, Vue sẽ tự động phát hiện thay đổi và cập nhật DOM tương ứng. Điều này có được nhờ một hệ thống tính phản ứng dựa trên cơ chế theo dõi dependency. Khi component được render lần đầu, Vue sẽ **theo dõi** mọi ref đã được dùng trong lần render đó. Về sau, khi ref bị thay đổi, nó sẽ **kích hoạt** render lại cho những component đang theo dõi nó.

Trong JavaScript thông thường, không có cách nào để phát hiện việc truy cập hay thay đổi của một biến thường. Tuy nhiên, ta có thể đánh chặn thao tác get và set trên property của object bằng getter và setter.

Property `.value` tạo cơ hội để Vue phát hiện khi ref bị truy cập hoặc bị thay đổi. Ở phía dưới, Vue thực hiện việc theo dõi trong getter của nó, và thực hiện việc kích hoạt trong setter. Về mặt ý tưởng, bạn có thể hình dung ref như một object kiểu sau:

```js
// mã giả, không phải triển khai thật sự
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

Một ưu điểm nữa của ref là khác với biến thường, bạn có thể truyền ref vào hàm mà vẫn giữ được quyền truy cập tới giá trị mới nhất và duy trì kết nối với hệ thống tính phản ứng. Điều này đặc biệt hữu ích khi tách logic phức tạp thành code có thể tái sử dụng.

Hệ thống tính phản ứng sẽ được bàn kỹ hơn trong phần [Tìm hiểu sâu về tính phản ứng](/guide/extras/reactivity-in-depth).
</div>

<div class="options-api">

## Khai báo method \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Bài học miễn phí về method trong Vue.js"/>

Để thêm method vào component instance, chúng ta dùng option `methods`. Nó phải là một object chứa những method mà ta muốn có:

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
    // method có thể được gọi trong hook vòng đời hoặc method khác
    this.increment()
  }
}
```

Vue sẽ tự động bind giá trị `this` cho `methods` để nó luôn trỏ tới component instance. Nhờ đó method vẫn giữ đúng `this` nếu được dùng làm event listener hoặc callback. Bạn nên tránh dùng arrow function khi định nghĩa `methods`, vì làm vậy sẽ khiến Vue không thể bind `this` một cách phù hợp:

```js
export default {
  methods: {
    increment: () => {
      // KHÔNG NÊN: ở đây sẽ không truy cập được `this`
    }
  }
}
```

Giống như các property khác của component instance, `methods` cũng có thể truy cập được từ template của component. Trong template, chúng thường được dùng nhiều nhất như event listener:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Thử trên Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

Trong ví dụ trên, method `increment` sẽ được gọi khi `<button>` được bấm.

</div>

### Tính phản ứng sâu {#deep-reactivity}

<div class="options-api">

Trong Vue, mặc định state có tính phản ứng sâu. Điều đó có nghĩa là thay đổi vẫn sẽ được phát hiện ngay cả khi bạn sửa object hoặc mảng lồng nhau:

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
      // những dòng này sẽ hoạt động đúng như mong đợi
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ref có thể chứa bất kỳ kiểu giá trị nào, bao gồm object lồng sâu, mảng, hoặc cấu trúc dữ liệu dựng sẵn của JavaScript như `Map`.

Ref sẽ làm cho giá trị của nó có tính phản ứng sâu. Điều đó có nghĩa là thay đổi vẫn được phát hiện ngay cả khi bạn sửa object hoặc mảng lồng nhau:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // những dòng này sẽ hoạt động đúng như mong đợi
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Những giá trị không phải nguyên thủy sẽ được biến thành proxy phản ứng thông qua [`reactive()`](#reactive), phần sẽ được nói ngay bên dưới.

Cũng có thể không dùng tính phản ứng sâu bằng [shallow ref](/api/reactivity-advanced#shallowref). Với shallow ref, chỉ việc truy cập `.value` mới được theo dõi để tạo phản ứng. Shallow ref hữu ích khi cần tối ưu hiệu năng bằng cách tránh chi phí quan sát các object lớn, hoặc trong trường hợp state bên trong được quản lý bởi thư viện bên ngoài.

Đọc thêm:

- [Giảm chi phí phản ứng cho cấu trúc bất biến lớn](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Tích hợp với hệ thống state bên ngoài](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Thời điểm cập nhật DOM {#dom-update-timing}

Khi bạn thay đổi state phản ứng, DOM sẽ được cập nhật tự động. Tuy nhiên, cần lưu ý rằng việc cập nhật DOM không được áp dụng đồng bộ ngay lập tức. Thay vào đó, Vue sẽ gom chúng lại cho tới "next tick" trong chu kỳ cập nhật để đảm bảo mỗi component chỉ cập nhật một lần, dù bạn có thay đổi state bao nhiêu lần đi nữa.

Để chờ việc cập nhật DOM hoàn tất sau khi state thay đổi, bạn có thể dùng global API [nextTick()](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Lúc này DOM đã được cập nhật
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
      // Lúc này DOM đã được cập nhật
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Có một cách khác để khai báo state phản ứng, đó là dùng API `reactive()`. Khác với ref là bọc giá trị bên trong một object đặc biệt, `reactive()` làm cho chính object đó trở nên phản ứng:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> Xem thêm: [Khai báo kiểu cho reactive](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

Cách dùng trong template:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Object phản ứng là [JavaScript Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) và hoạt động gần như object bình thường. Điểm khác biệt là Vue có thể chặn việc truy cập và thay đổi mọi property của object phản ứng để theo dõi và kích hoạt cập nhật.

`reactive()` chuyển object thành phản ứng ở mức sâu: các object lồng nhau cũng sẽ được bọc bằng `reactive()` khi bị truy cập. Nó cũng được `ref()` gọi ở bên trong khi giá trị của ref là object. Tương tự shallow ref, cũng có API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) nếu bạn muốn không dùng tính phản ứng sâu.

### Proxy phản ứng và object gốc \*\* {#reactive-proxy-vs-original-1}

Điều quan trọng cần nhớ là giá trị trả về từ `reactive()` là một [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) của object gốc, nên nó không bằng object gốc:

```js
const raw = {}
const proxy = reactive(raw)

// proxy KHÔNG bằng object gốc.
console.log(proxy === raw) // false
```

Chỉ proxy mới có tính phản ứng, còn việc thay đổi object gốc sẽ không kích hoạt cập nhật. Vì vậy, thực hành tốt nhất khi làm việc với hệ thống tính phản ứng của Vue là **chỉ dùng bản proxy của state**.

Để đảm bảo việc truy cập proxy luôn nhất quán, gọi `reactive()` trên cùng một object sẽ luôn trả về cùng một proxy, và gọi `reactive()` trên một proxy đã có cũng sẽ trả về chính proxy đó:

```js
// gọi reactive() trên cùng một object sẽ trả về cùng một proxy
console.log(reactive(raw) === proxy) // true

// gọi reactive() trên một proxy sẽ trả về chính nó
console.log(reactive(proxy) === proxy) // true
```

Quy tắc này cũng áp dụng cho object lồng nhau. Vì có tính phản ứng sâu nên object lồng trong object phản ứng cũng là proxy:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Giới hạn của `reactive()` \*\* {#limitations-of-reactive}

API `reactive()` có một vài giới hạn:

1. **Giới hạn kiểu giá trị:** nó chỉ hoạt động với kiểu object, gồm object, array và [kiểu collection](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) như `Map` và `Set`. Nó không thể giữ [kiểu nguyên thủy](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) như `string`, `number` hay `boolean`.

2. **Không thể thay cả object:** vì cơ chế theo dõi phản ứng của Vue hoạt động thông qua việc truy cập property, chúng ta luôn phải giữ cùng một tham chiếu tới object phản ứng. Điều đó có nghĩa là không thể dễ dàng "thay" một object phản ứng, vì kết nối phản ứng với tham chiếu đầu tiên sẽ bị mất:

   ```js
   let state = reactive({ count: 0 })

   // tham chiếu ở trên ({ count: 0 }) không còn được theo dõi nữa
   // (kết nối phản ứng đã bị mất)
   state = reactive({ count: 1 })
   ```

3. **Không thân thiện với destructuring:** khi ta destructure property nguyên thủy của object phản ứng thành biến cục bộ, hoặc truyền property đó vào một hàm, ta sẽ làm mất kết nối phản ứng:

   ```js
   const state = reactive({ count: 0 })

   // count bị tách khỏi state.count khi destructure
   let { count } = state
   // không ảnh hưởng tới state gốc
   count++

   // hàm này nhận vào một số thông thường
   // nên sẽ không theo dõi được thay đổi của state.count
   // phải truyền cả object vào nếu muốn giữ tính phản ứng
   callSomeFunction(state.count)
   ```

Do những giới hạn này, chúng tôi khuyên dùng `ref()` làm API chính để khai báo state phản ứng.

## Chi tiết thêm về việc mở bọc ref \*\* {#additional-ref-unwrapping-details}

### Khi là property của object phản ứng \*\* {#ref-unwrapping-as-reactive-object-property}

Ref sẽ được tự động mở bọc khi bị truy cập hoặc thay đổi dưới dạng property của object phản ứng. Nói cách khác, nó sẽ hoạt động như một property thông thường:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Nếu một ref mới được gán vào property đang liên kết với một ref hiện có, nó sẽ thay thế ref cũ:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// ref gốc giờ đã tách khỏi state.count
console.log(count.value) // 1
```

Việc mở bọc ref chỉ xảy ra khi ref nằm bên trong object phản ứng sâu. Nó không áp dụng khi ref được truy cập như property của [shallow reactive object](/api/reactivity-advanced#shallowreactive).

### Lưu ý với mảng và collection \*\* {#caveat-in-arrays-and-collections}

Khác với object phản ứng, sẽ **không có** việc mở bọc khi ref được truy cập như một phần tử của mảng phản ứng hoặc collection gốc như `Map`:

```js
const books = reactive([ref('Vue 3 Guide')])
// ở đây cần dùng .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// ở đây cũng cần dùng .value
console.log(map.get('count').value)
```

### Lưu ý khi mở bọc trong template \*\* {#caveat-when-unwrapping-in-templates}

Việc mở bọc ref trong template chỉ áp dụng nếu ref là property cấp cao nhất trong ngữ cảnh render của template.

Trong ví dụ dưới đây, `count` và `object` là property cấp cao nhất, nhưng `object.id` thì không phải:

```js
const count = ref(0)
const object = { id: ref(1) }
```

Vì vậy, biểu thức này hoạt động đúng như mong đợi:

```vue-html
{{ count + 1 }}
```

...còn biểu thức này thì **KHÔNG**:

```vue-html
{{ object.id + 1 }}
```

Kết quả render sẽ là `[object Object]1` vì `object.id` không được mở bọc khi biểu thức được tính và vẫn là một object ref. Để sửa điều này, ta có thể destructure `id` thành một property cấp cao nhất:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Bây giờ kết quả render sẽ là `2`.

Một điều nữa cần lưu ý là ref vẫn được mở bọc nếu nó là giá trị cuối cùng sau khi tính xong của một nội suy văn bản, tức là trong thẻ <code v-pre>{{ }}</code>. Vì vậy, ví dụ sau sẽ render ra `1`:

```vue-html
{{ object.id }}
```

Đây chỉ là một tiện ích của nội suy văn bản và tương đương với <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Method có state \* {#stateful-methods}

Trong một số trường hợp, ta cần tạo động một hàm method, ví dụ tạo một event handler có debounce:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // debounce với Lodash
    click: debounce(function () {
      // ... xử lý khi click ...
    }, 500)
  }
}
```

Tuy nhiên, cách này gây vấn đề với những component được dùng lại vì một hàm debounce là **hàm có state**: nó giữ một số trạng thái nội bộ về thời gian đã trôi qua. Nếu nhiều component instance cùng chia sẻ một hàm debounce, chúng sẽ ảnh hưởng lẫn nhau.

Để giữ cho hàm debounce của mỗi component instance độc lập với nhau, ta có thể tạo phiên bản debounce trong hook vòng đời `created`:

```js
export default {
  created() {
    // mỗi instance giờ có một bản riêng của debounced handler
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // cũng nên hủy timer
    // khi component bị gỡ bỏ
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... xử lý khi click ...
    }
  }
}
```

</div>
