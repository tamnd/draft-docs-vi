## outline: deep

# Nền tảng Reactivity {#reactivity-fundamentals}

:::tip Ưu tiên API
Trang này và nhiều chương khác trong hướng dẫn có nội dung khác nhau cho Options API và Composition API. Tùy chọn hiện tại của bạn là <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Bạn có thể chuyển đổi giữa các kiểu API bằng công tắc "API Preference" ở phía trên thanh bên trái.
:::

<div class="options-api">

## Khai báo state reactive * {#declaring-reactive-state}

Với Options API, chúng ta dùng option `data` để khai báo state reactive của component. Giá trị của option này phải là một function trả về một object. Vue sẽ gọi function này khi tạo instance component mới, và bọc object trả về trong hệ thống reactivity của nó. Mọi property cấp cao nhất của object này sẽ được proxy lên instance component (`this` trong methods và lifecycle hooks):

```js id="a9s3kt"
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

```js id="q8l2wm"
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

Khi bạn truy cập `this.someObject` sau khi gán, giá trị đó là một reactive proxy của `newObject` gốc. Không giống Vue 2, `newObject` gốc vẫn giữ nguyên và không được làm reactive. Hãy luôn truy cập state reactive thông qua `this`.

</div>

<div class="composition-api">

## Khai báo state reactive ** {#declaring-reactive-state-1}

### `ref()` ** {#ref}

Trong Composition API, cách được khuyến nghị để khai báo state reactive là dùng function `ref()`:

```js id="h4m7zc"
import { ref } from 'vue'

const count = ref(0)
```

`ref()` nhận một tham số và trả về một object ref chứa giá trị đó trong property `.value`:

```js id="x2n8tp"
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

Để truy cập ref trong template của component, hãy khai báo và return chúng từ function `setup()`:

```js id="c3k9va"
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    return {
      count
    }
  }
}
```

```vue-html id="v7d1os"
<div>{{ count }}</div>
```

Lưu ý rằng chúng ta không cần thêm `.value` khi dùng ref trong template. Ref sẽ tự động được unwrap khi dùng trong template.

Bạn cũng có thể mutate ref trực tiếp trong event handler:

```vue-html id="y9b3zr"
<button @click="count++">
  {{ count }}
</button>
```

Với logic phức tạp hơn, chúng ta có thể khai báo function thao tác với ref trong cùng scope và expose chúng như method:

```js id="t5w8lk"
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      count.value++
    }

    return {
      count,
      increment
    }
  }
}
```

Các method này có thể dùng làm event handler:

```vue-html id="p1z7dx"
<button @click="increment">
  {{ count }}
</button>
```

### `<script setup>` ** {#script-setup}

Việc expose state và method thủ công qua `setup()` có thể dài dòng. Có thể tránh bằng cách dùng `<script setup>`:

```vue id="k8c2px"
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

Import, biến và function ở top-level trong `<script setup>` sẽ tự động dùng được trong template.

### Tại sao cần ref? ** {#why-refs}

Khi dùng ref trong template và thay đổi giá trị, Vue sẽ tự động phát hiện và cập nhật DOM.

Vue dùng hệ thống theo dõi dependency. Khi render, Vue track ref. Khi ref thay đổi, Vue trigger render lại.

Trong JavaScript thường, không thể phát hiện truy cập biến. Nhưng với object, có thể dùng getter/setter.

`.value` cho phép Vue track và trigger:

```js id="n2d9vk"
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

Ref có thể truyền qua function mà vẫn giữ reactivity.

</div>

<div class="options-api">

## Khai báo Methods * {#declaring-methods}

Để thêm method, dùng `methods`:

```js id="u6q8df"
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
    this.increment()
  }
}
```

Vue tự động bind `this`. Không dùng arrow function.

Methods dùng trong template:

```vue-html id="o9k3xa"
<button @click="increment">{{ count }}</button>
```

</div>

### Deep Reactivity {#deep-reactivity}

<div class="options-api">

State mặc định deep reactive:

```js id="m4r8yz"
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
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ref hỗ trợ object lồng nhau:

```js id="s7z2qn"
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})
```

</div>

### Thời điểm cập nhật DOM {#dom-update-timing}

DOM update không đồng bộ. Vue batch update.

Dùng `nextTick()` để chờ:

```js id="d2n7fh"
import { nextTick } from 'vue'
```

</div>

