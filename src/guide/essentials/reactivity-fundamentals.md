---
outline: deep
---

# Nền tảng Reactivity {#reactivity-fundamentals}

:::tip Ưu tiên API
Trang này và nhiều chương khác trong hướng dẫn có nội dung khác nhau cho Options API và Composition API. Tùy chọn hiện tại của bạn là <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Bạn có thể chuyển đổi giữa các kiểu API bằng công tắc "API Preference" ở phía trên thanh bên trái.
:::

<div class="options-api">

## Khai báo state reactive * {#declaring-reactive-state}

Với Options API, chúng ta dùng option `data` để khai báo state reactive của component. Giá trị của option này phải là một function trả về một object. Vue sẽ gọi function này khi tạo instance component mới, và bọc object trả về trong hệ thống reactivity của nó. Mọi property cấp cao nhất của object này sẽ được proxy lên instance component (`this` trong methods và lifecycle hooks):

```js
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` là một lifecycle hook sẽ được giải thích sau
  mounted() {
    // `this` trỏ tới instance của component.
    console.log(this.count) // => 1

    // data cũng có thể bị thay đổi
    this.count = 2
  }
}
```

[Thử trong Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Các property của instance này chỉ được thêm vào khi instance được tạo lần đầu, vì vậy bạn cần đảm bảo tất cả chúng đều có trong object trả về từ function `data`. Khi cần, hãy dùng `null`, `undefined` hoặc giá trị placeholder khác cho các property mà giá trị mong muốn chưa có sẵn.

Bạn có thể thêm property mới trực tiếp vào `this` mà không khai báo trong `data`. Tuy nhiên, các property thêm theo cách này sẽ không thể kích hoạt cập nhật reactive.

Vue dùng tiền tố `$` khi expose các API built-in của nó thông qua instance component. Nó cũng dành riêng tiền tố `_` cho các property nội bộ. Bạn nên tránh dùng các tên property cấp cao nhất trong `data` bắt đầu bằng các ký tự này.

### Reactive Proxy so với giá trị gốc * {#reactive-proxy-vs-original}

Trong Vue 3, dữ liệu được làm reactive bằng cách sử dụng [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Những người dùng từ Vue 2 cần lưu ý edge case sau:

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

Khi bạn truy cập `this.someObject` sau khi gán, giá trị đó là một reactive proxy (proxy phản ứng) của `newObject` gốc. Không giống Vue 2, `newObject` gốc vẫn giữ nguyên và không được làm reactive. Hãy luôn truy cập state reactive thông qua `this`.

</div>

<div class="composition-api">

## Khai báo state reactive ** {#declaring-reactive-state-1}

### `ref()` ** {#ref}

Trong Composition API, cách được khuyến nghị để khai báo state reactive là dùng function [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` nhận một tham số và trả về một object ref chứa giá trị đó trong property `.value`:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Xem thêm: [Typing Refs](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Để truy cập ref trong template của component, hãy khai báo và return chúng từ function `setup()`:

```js
import { ref } from 'vue'

export default {
  // `setup` là một hook đặc biệt dành cho Composition API.
  setup() {
    const count = ref(0)

    // expose ref ra template
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Lưu ý rằng chúng ta **không** cần thêm `.value` khi dùng ref trong template. Ref sẽ tự động được unwrap (mở bọc ref) khi dùng trong template (với một số [lưu ý](#caveat-when-unwrapping-in-templates)).

Bạn cũng có thể mutate ref trực tiếp trong event handler:

```vue-html
<button @click="count++">
  {{ count }}
</button>
```

Với logic phức tạp hơn, chúng ta có thể khai báo function thao tác với ref trong cùng scope và expose chúng như method:

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value cần thiết trong JavaScript
      count.value++
    }

    // đừng quên expose cả function.
    return {
      count,
      increment
    }
  }
}
```

Các method này có thể dùng làm event handler:

```vue-html
<button @click="increment">
  {{ count }}
</button>
```

### `<script setup>` ** {#script-setup}

Việc expose state và method thủ công qua `setup()` có thể dài dòng. Có thể tránh bằng cách dùng [Single-File Components (SFCs)](/guide/scaling-up/sfc). Chúng ta có thể đơn giản hóa bằng `<script setup>`:

```vue
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

Import, biến và function ở top-level trong `<script setup>` sẽ tự động dùng được trong template. Hãy hình dung template như một JavaScript function được khai báo trong cùng scope — nó tự nhiên có quyền truy cập vào mọi thứ được khai báo cùng với nó.

:::tip
Trong phần còn lại của hướng dẫn, chúng ta sẽ chủ yếu dùng cú pháp SFC + `<script setup>` cho các ví dụ code Composition API, vì đây là cách dùng phổ biến nhất với các developer Vue.

Nếu bạn không dùng SFC, bạn vẫn có thể dùng Composition API với option [`setup()`](/api/composition-api-setup).
:::

### Tại sao cần ref? ** {#why-refs}

Bạn có thể thắc mắc tại sao chúng ta cần ref với `.value` thay vì dùng biến thông thường. Để giải thích điều đó, chúng ta cần tìm hiểu sơ lược về cách hệ thống reactivity của Vue hoạt động.

Khi bạn dùng ref trong template và sau đó thay đổi giá trị của ref, Vue sẽ tự động phát hiện thay đổi và cập nhật DOM. Điều này được thực hiện bằng một hệ thống reactivity dựa trên theo dõi dependency. Khi một component được render lần đầu, Vue **track** mọi ref được dùng trong quá trình render đó. Về sau, khi một ref bị mutate, nó sẽ **trigger** re-render cho các component đang theo dõi nó.

Trong JavaScript thông thường, không có cách nào để phát hiện việc truy cập hay thay đổi của các biến thông thường. Tuy nhiên, chúng ta có thể chặn các thao tác get và set của property trong một object bằng getter và setter.

Property `.value` cho Vue cơ hội phát hiện khi nào một ref được truy cập hoặc bị thay đổi. Vue thực hiện tracking trong getter và triggering trong setter. Về mặt khái niệm, bạn có thể hình dung một ref như một object trông như thế này:

```js
// mã giả, không phải cài đặt thực tế
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

Một điểm hay khác của ref là, không giống các biến thông thường, bạn có thể truyền ref vào function trong khi vẫn giữ được quyền truy cập giá trị mới nhất và kết nối reactivity. Điều này đặc biệt hữu ích khi refactor logic phức tạp thành code có thể tái sử dụng.

Hệ thống reactivity được thảo luận chi tiết hơn trong phần [Reactivity in Depth](/guide/extras/reactivity-in-depth).

</div>

<div class="options-api">

## Khai báo Methods * {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Free Vue.js Methods Lesson"/>

Để thêm method vào instance component, dùng option `methods`. Đây phải là một object chứa các method mong muốn:

```js
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
    // methods có thể được gọi trong lifecycle hooks, hoặc trong methods khác!
    this.increment()
  }
}
```

Vue tự động bind giá trị `this` cho `methods` để nó luôn trỏ tới instance component. Điều này đảm bảo method giữ đúng giá trị `this` khi được dùng làm event listener hoặc callback. Bạn nên tránh dùng arrow function khi định nghĩa `methods`, vì điều đó ngăn Vue bind đúng giá trị `this`:

```js
export default {
  methods: {
    increment: () => {
      // XẤU: không truy cập được `this` ở đây!
    }
  }
}
```

Giống như mọi property khác của instance component, các `methods` đều có thể truy cập từ trong template của component. Trong template, chúng thường được dùng làm event listener:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Thử trong Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

Trong ví dụ trên, method `increment` sẽ được gọi khi `<button>` được click.

</div>

### Deep Reactivity {#deep-reactivity}

<div class="options-api">

Trong Vue, state mặc định là deep reactive. Điều này có nghĩa là bạn có thể kỳ vọng các thay đổi sẽ được phát hiện ngay cả khi bạn mutate object hoặc array lồng nhau:

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
      // các thao tác này sẽ hoạt động như mong đợi.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ref có thể chứa bất kỳ kiểu giá trị nào, bao gồm object lồng nhau sâu, array, hay các cấu trúc dữ liệu built-in của JavaScript như `Map`.

Một ref sẽ làm cho giá trị của nó deep reactive. Điều này có nghĩa là bạn có thể kỳ vọng các thay đổi sẽ được phát hiện ngay cả khi bạn mutate object hoặc array lồng nhau:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // các thao tác này sẽ hoạt động như mong đợi.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Các giá trị non-primitive sẽ được chuyển thành reactive proxy (proxy phản ứng) thông qua [`reactive()`](#reactive), được thảo luận bên dưới.

Cũng có thể opt-out khỏi deep reactivity bằng [shallow refs](/api/reactivity-advanced#shallowref). Với shallow refs, chỉ có quyền truy cập `.value` mới được track cho reactivity. Shallow refs có thể dùng để tối ưu hiệu năng bằng cách tránh chi phí theo dõi các object lớn, hoặc trong trường hợp inner state được quản lý bởi thư viện bên ngoài.

Đọc thêm:

- [Giảm chi phí Reactivity cho các cấu trúc Immutable lớn](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Tích hợp với External State Systems](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Thời điểm cập nhật DOM {#dom-update-timing}

Khi bạn mutate state reactive, DOM sẽ được cập nhật tự động. Tuy nhiên, cần lưu ý rằng các cập nhật DOM không được áp dụng đồng bộ. Thay vào đó, Vue buffer chúng lại đến "next tick" trong chu kỳ cập nhật để đảm bảo mỗi component chỉ cập nhật một lần dù bạn đã thực hiện bao nhiêu thay đổi state.

Để chờ DOM cập nhật hoàn tất sau khi thay đổi state, bạn có thể dùng global API [nextTick()](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Bây giờ DOM đã được cập nhật
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
      // Bây giờ DOM đã được cập nhật
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` ** {#reactive}

Có một cách khác để khai báo state reactive, với API `reactive()`. Không giống ref, vốn bọc giá trị bên trong trong một object đặc biệt, `reactive()` làm cho chính object đó trở thành reactive:

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

Reactive object là [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) và hoạt động giống như object thông thường. Điểm khác biệt là Vue có thể chặn việc truy cập và mutate mọi property của reactive object để theo dõi và kích hoạt reactivity.

`reactive()` chuyển đổi object một cách sâu (deep): các object lồng nhau cũng được bọc bằng `reactive()` khi truy cập. Nó cũng được gọi nội bộ bởi `ref()` khi giá trị ref là một object. Tương tự shallow refs, cũng có API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) để opt-out khỏi deep reactivity.

### Reactive Proxy so với giá trị gốc ** {#reactive-proxy-vs-original-1}

Cần lưu ý rằng giá trị trả về từ `reactive()` là một [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) của object gốc, không bằng object gốc:

```js
const raw = {}
const proxy = reactive(raw)

// proxy KHÔNG bằng object gốc.
console.log(proxy === raw) // false
```

Chỉ có proxy mới là reactive — việc mutate object gốc sẽ không trigger cập nhật. Do đó, best practice khi làm việc với hệ thống reactivity của Vue là **chỉ sử dụng các phiên bản proxied của state**.

Để đảm bảo truy cập nhất quán tới proxy, gọi `reactive()` trên cùng một object luôn trả về cùng một proxy, và gọi `reactive()` trên một proxy hiện có cũng trả về chính proxy đó:

```js
// gọi reactive() trên cùng một object trả về cùng một proxy
console.log(reactive(raw) === proxy) // true

// gọi reactive() trên một proxy trả về chính nó
console.log(reactive(proxy) === proxy) // true
```

Quy tắc này cũng áp dụng cho các object lồng nhau. Do deep reactivity, các object lồng nhau bên trong reactive object cũng là proxy:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Các giới hạn của `reactive()` ** {#limitations-of-reactive}

API `reactive()` có một số giới hạn:

1. **Giới hạn kiểu giá trị:** nó chỉ hoạt động với kiểu object (objects, arrays, và [collection types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) như `Map` và `Set`). Nó không thể chứa [primitive types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) như `string`, `number` hay `boolean`.

2. **Không thể thay thế toàn bộ object:** vì theo dõi reactivity của Vue hoạt động dựa trên quyền truy cập property, chúng ta phải luôn giữ cùng một tham chiếu đến reactive object. Điều này có nghĩa là chúng ta không thể dễ dàng "thay thế" một reactive object vì kết nối reactivity với tham chiếu đầu tiên sẽ bị mất:

   ```js
   let state = reactive({ count: 0 })

   // tham chiếu trên ({ count: 0 }) không còn được track nữa
   // (kết nối reactivity bị mất!)
   state = reactive({ count: 1 })
   ```

3. **Không thân thiện với destructuring:** khi chúng ta destructure property kiểu primitive của reactive object thành biến local, hay khi chúng ta truyền property đó vào một function, chúng ta sẽ mất kết nối reactivity:

   ```js
   const state = reactive({ count: 0 })

   // count bị ngắt kết nối khỏi state.count khi destructure.
   let { count } = state
   // không ảnh hưởng đến state gốc
   count++

   // function nhận một số thông thường và
   // sẽ không thể track thay đổi của state.count
   // chúng ta phải truyền toàn bộ object để giữ reactivity
   callSomeFunction(state.count)
   ```

Do những giới hạn này, chúng ta khuyến nghị dùng `ref()` làm API chính để khai báo state reactive.

## Chi tiết bổ sung về Ref Unwrapping {#additional-ref-unwrapping-details}

### Khi là property của Reactive Object ** {#ref-unwrapping-as-reactive-object-property}

Một ref sẽ tự động được unwrap (mở bọc ref) khi được truy cập hoặc mutate như một property của reactive object. Nói cách khác, nó hoạt động giống như property thông thường:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Nếu một ref mới được gán cho property đã liên kết với ref hiện có, nó sẽ thay thế ref cũ:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// ref gốc đã bị ngắt kết nối khỏi state.count
console.log(count.value) // 1
```

Ref unwrapping (mở bọc ref) chỉ xảy ra khi được lồng bên trong một deep reactive object. Nó không áp dụng khi được truy cập như property của [shallow reactive object](/api/reactivity-advanced#shallowreactive).

### Lưu ý trong Arrays và Collections ** {#caveat-in-arrays-and-collections}

Không giống reactive object, **không** có unwrapping nào được thực hiện khi ref được truy cập như một phần tử của reactive array hoặc collection type gốc như `Map`:

```js
const books = reactive([ref('Vue 3 Guide')])
// cần .value ở đây
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// cần .value ở đây
console.log(map.get('count').value)
```

### Lưu ý khi Unwrapping trong Templates ** {#caveat-when-unwrapping-in-templates}

Ref unwrapping (mở bọc ref) trong template chỉ áp dụng nếu ref là property cấp cao nhất trong context render của template.

Trong ví dụ dưới, `count` và `object` là property cấp cao nhất, nhưng `object.id` thì không:

```js
const count = ref(0)
const object = { id: ref(1) }
```

Do đó, biểu thức này hoạt động như mong đợi:

```vue-html
{{ count + 1 }}
```

...trong khi cái này thì **KHÔNG**:

```vue-html
{{ object.id + 1 }}
```

Kết quả render sẽ là `[object Object]1` vì `object.id` không được unwrap khi đánh giá biểu thức và vẫn là một ref object. Để sửa điều này, chúng ta có thể destructure `id` thành property cấp cao nhất:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Bây giờ kết quả render sẽ là `2`.

Một điều cần lưu ý nữa là ref sẽ được unwrap nếu đó là giá trị được đánh giá cuối cùng của một text interpolation (tức là tag <code v-pre>{{ }}</code>), vì vậy sau đây sẽ render `1`:

```vue-html
{{ object.id }}
```

Đây chỉ là tính năng tiện lợi của text interpolation và tương đương với <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Stateful Methods * {#stateful-methods}

Trong một số trường hợp, chúng ta có thể cần tạo động một method function, ví dụ tạo debounced event handler:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Debouncing với Lodash
    click: debounce(function () {
      // ... xử lý click ...
    }, 500)
  }
}
```

Tuy nhiên, cách này có vấn đề với các component được tái sử dụng vì debounced function là **stateful**: nó duy trì một số internal state về thời gian đã trôi qua. Nếu nhiều instance component dùng chung một debounced function, chúng sẽ can thiệp lẫn nhau.

Để giữ cho debounced function của mỗi instance component độc lập với nhau, chúng ta có thể tạo phiên bản debounced trong lifecycle hook `created`:

```js
export default {
  created() {
    // mỗi instance bây giờ có bản sao riêng của debounced handler
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // cũng là ý hay khi hủy timer
    // khi component bị xóa
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... xử lý click ...
    }
  }
}
```

</div>
