# Provide / Inject {#provide-inject}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

## Truyền Prop Qua Nhiều Tầng {#prop-drilling}

Thông thường, khi cần truyền dữ liệu từ component cha sang component con, ta sẽ dùng [props](/guide/components/props). Tuy nhiên, hãy tưởng tượng trường hợp ta có một cây component lớn và một component lồng rất sâu cần một thứ gì đó từ một component tổ tiên ở khá xa. Nếu chỉ dùng props, ta sẽ buộc phải truyền cùng một prop xuyên suốt toàn bộ chuỗi component cha:

![prop drilling diagram](./images/prop-drilling.png)

<!-- https://www.figma.com/file/yNDTtReM2xVgjcGVRzChss/prop-drilling -->

Hãy lưu ý rằng dù component `<Footer>` có thể hoàn toàn không cần đến những prop này, nó vẫn phải khai báo và chuyển tiếp chúng chỉ để `<DeepChild>` có thể truy cập được. Nếu chuỗi component cha còn dài hơn nữa, nhiều component khác trên đường truyền cũng sẽ bị ảnh hưởng. Điều này được gọi là "prop drilling" và rõ ràng là không mấy dễ chịu.

Ta có thể giải quyết prop drilling bằng `provide` và `inject`. Một component cha có thể đóng vai trò **dependency provider** cho toàn bộ hậu duệ của nó. Bất kỳ component nào trong cây hậu duệ, bất kể sâu đến đâu, đều có thể **inject** dependency do các component ở phía trên trong chuỗi cha cung cấp.

![Provide/inject scheme](./images/provide-inject.png)

<!-- https://www.figma.com/file/PbTJ9oXis5KUawEOWdy2cE/provide-inject -->

## Provide {#provide}

<div class="composition-api">

Để cung cấp dữ liệu cho các component hậu duệ, hãy dùng hàm [`provide()`](/api/composition-api-dependency-injection#provide):

```vue
<script setup>
import { provide } from 'vue'

provide(/* key */ 'message', /* value */ 'hello!')
</script>
```

Nếu không dùng `<script setup>`, hãy chắc chắn rằng `provide()` được gọi một cách đồng bộ bên trong `setup()`:

```js
import { provide } from 'vue'

export default {
  setup() {
    provide(/* key */ 'message', /* value */ 'hello!')
  }
}
```

Hàm `provide()` nhận hai đối số. Đối số thứ nhất được gọi là **injection key**, có thể là chuỗi hoặc `Symbol`. Injection key được component hậu duệ dùng để tra cứu giá trị muốn inject. Một component có thể gọi `provide()` nhiều lần với các injection key khác nhau để cung cấp nhiều giá trị khác nhau.

Đối số thứ hai là giá trị được cung cấp. Giá trị này có thể thuộc bất kỳ kiểu nào, bao gồm cả state phản ứng như ref:

```js
import { ref, provide } from 'vue'

const count = ref(0)
provide('key', count)
```

Việc cung cấp giá trị phản ứng cho phép component hậu duệ dùng giá trị đó tạo ra kết nối phản ứng với component provider.

</div>

<div class="options-api">

Để cung cấp dữ liệu cho component hậu duệ, hãy dùng option [`provide`](/api/options-composition#provide):

```js
export default {
  provide: {
    message: 'hello!'
  }
}
```

Với mỗi property trong object `provide`, key được component con dùng để tra cứu giá trị cần inject, còn value là thứ sẽ thực sự được inject.

Nếu cần cung cấp state theo từng instance, ví dụ dữ liệu được khai báo qua `data()`, thì `provide` phải dùng giá trị là hàm:

```js{7-12}
export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    // dùng cú pháp hàm để có thể truy cập `this`
    return {
      message: this.message
    }
  }
}
```

Tuy nhiên, hãy lưu ý rằng cách này **không** làm cho injection trở thành reactive. Phần [làm cho injection có tính phản ứng](#working-with-reactivity) sẽ được bàn ở bên dưới.

</div>

## Provide Ở Cấp Ứng Dụng {#app-level-provide}

Ngoài việc cung cấp dữ liệu bên trong một component, ta cũng có thể provide ở cấp ứng dụng:

```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hello!')
```

Giá trị provide ở cấp ứng dụng sẽ khả dụng với mọi component được render trong app. Điều này đặc biệt hữu ích khi viết [plugin](/guide/reusability/plugins), vì plugin thường không thể provide giá trị thông qua component.

## Inject {#inject}

<div class="composition-api">

Để inject dữ liệu do component tổ tiên provide, hãy dùng hàm [`inject()`](/api/composition-api-dependency-injection#inject):

```vue
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```

Nếu có nhiều component cha cùng provide dữ liệu với cùng một key, `inject` sẽ lấy giá trị từ component cha gần nhất trong chuỗi cha của component hiện tại.

Nếu giá trị được provide là một ref, nó sẽ được inject nguyên trạng và **không** bị tự động mở bọc. Điều này cho phép component injector giữ nguyên kết nối phản ứng với component provider.

[Ví dụ đầy đủ về provide + inject có tính phản ứng](https://play.vuejs.org/#eNqFUUFugzAQ/MrKF1IpxfeIVKp66Kk/8MWFDXYFtmUbpArx967BhURRU9/WOzO7MzuxV+fKcUB2YlWovXYRAsbBvQije2d9hAk8Xo7gvB11gzDDxdseCuIUG+ZN6a7JjZIvVRIlgDCcw+d3pmvTglz1okJ499I0C3qB1dJQT9YRooVaSdNiACWdQ5OICj2WwtTWhAg9hiBbhHNSOxQKu84WT8LkNQ9FBhTHXyg1K75aJHNUROxdJyNSBVBp44YI43NvG+zOgmWWYGt7dcipqPhGZEe2ef07wN3lltD+lWN6tNkV/37+rdKjK2rzhRTt7f3u41xhe37/xJZGAL2PLECXa9NKdD/a6QTTtGnP88LgiXJtYv4BaLHhvg==)

Một lần nữa, nếu không dùng `<script setup>`, `inject()` chỉ nên được gọi đồng bộ bên trong `setup()`:

```js
import { inject } from 'vue'

export default {
  setup() {
    const message = inject('message')
    return { message }
  }
}
```

</div>

<div class="options-api">

Để inject dữ liệu do component tổ tiên provide, hãy dùng option [`inject`](/api/options-composition#inject):

```js
export default {
  inject: ['message'],
  created() {
    console.log(this.message) // giá trị được inject
  }
}
```

Các injection được resolve **trước** state riêng của component, vì vậy bạn có thể truy cập các property đã inject trong `data()`:

```js
export default {
  inject: ['message'],
  data() {
    return {
      // dữ liệu khởi tạo dựa trên giá trị được inject
      fullMessage: this.message
    }
  }
}
```

Nếu có nhiều component cha cùng provide dữ liệu với cùng một key, `inject` sẽ lấy giá trị từ component cha gần nhất trong chuỗi cha của component hiện tại.

[Ví dụ đầy đủ về provide + inject](https://play.vuejs.org/#eNqNkcFqwzAQRH9l0EUthOhuRKH00FO/oO7B2JtERZaEvA4F43+vZCdOTAIJCImRdpi32kG8h7A99iQKobs6msBvpTNt8JHxcTC2wS76FnKrJpVLZelKR39TSUO7qreMoXRA7ZPPkeOuwHByj5v8EqI/moZeXudCIBL30Z0V0FLXVXsqIA9krU8R+XbMR9rS0mqhS4KpDbZiSgrQc5JKQqvlRWzEQnyvuc9YuWbd4eXq+TZn0IvzOeKr8FvsNcaK/R6Ocb9Uc4FvefpE+fMwP0wH8DU7wB77nIo6x6a2hvNEME5D0CpbrjnHf+8excI=)

### Đặt Bí Danh Cho Injection \* {#injection-aliasing}

Khi dùng cú pháp mảng cho `inject`, các property được inject sẽ được lộ ra trên instance component với cùng key đó. Trong ví dụ trên, property được provide dưới key `"message"` và được inject thành `this.message`. Local key lúc này trùng với injection key.

Nếu muốn inject property đó với một local key khác, ta cần dùng cú pháp object cho option `inject`:

```js
export default {
  inject: {
    /* local key */ localMessage: {
      from: /* injection key */ 'message'
    }
  }
}
```

Ở đây, component sẽ tìm property được provide dưới key `"message"`, rồi lộ nó ra dưới dạng `this.localMessage`.

</div>

### Giá Trị Mặc Định Cho Injection {#injection-default-values}

Mặc định, `inject` giả định rằng injection key đã được provide ở đâu đó trong chuỗi cha. Nếu key không được provide, Vue sẽ cảnh báo lúc chạy.

Nếu muốn một property được inject hoạt động với provider tùy chọn, ta cần khai báo giá trị mặc định, tương tự như với props:

<div class="composition-api">

```js
// `value` sẽ là "default value"
// nếu không có dữ liệu nào khớp với "message" được provide
const value = inject('message', 'default value')
```

Trong một số trường hợp, giá trị mặc định cần được tạo bằng cách gọi hàm hoặc khởi tạo một class mới. Để tránh tính toán hoặc side effect không cần thiết nếu giá trị tùy chọn đó không được dùng đến, ta có thể dùng factory function để tạo giá trị mặc định:

```js
const value = inject('key', () => new ExpensiveClass(), true)
```

Đối số thứ ba cho biết giá trị mặc định nên được xem là factory function.

</div>

<div class="options-api">

```js
export default {
  // bắt buộc phải dùng cú pháp object
  // khi khai báo giá trị mặc định cho injection
  inject: {
    message: {
      from: 'message', // phần này là tùy chọn nếu dùng cùng key cho injection
      default: 'default value'
    },
    user: {
      // dùng factory function cho giá trị không nguyên thủy mà việc tạo ra tốn kém
      // hoặc cần là duy nhất cho từng instance component.
      default: () => ({ name: 'John' })
    }
  }
}
```

</div>

## Làm Việc Với Tính Phản Ứng {#working-with-reactivity}

<div class="composition-api">

Khi dùng giá trị provide / inject dạng phản ứng, **chúng tôi khuyến nghị nên giữ mọi thao tác thay đổi reactive state bên trong _provider_ bất cứ khi nào có thể**. Điều này bảo đảm state được provide và các cách thay đổi nó được đặt cùng nhau trong cùng một component, nhờ đó về sau dễ bảo trì hơn.

Sẽ có lúc ta cần cập nhật dữ liệu từ component injector. Trong những trường hợp đó, chúng tôi khuyến nghị provide một hàm chịu trách nhiệm thay đổi state:

```vue{7-9,13}
<!-- bên trong component provider -->
<script setup>
import { provide, ref } from 'vue'

const location = ref('North Pole')

function updateLocation() {
  location.value = 'South Pole'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue{5}
<!-- trong component injector -->
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

Cuối cùng, bạn có thể bọc giá trị được provide bằng [`readonly()`](/api/reactivity-core#readonly) nếu muốn bảo đảm rằng dữ liệu truyền qua `provide` không thể bị component injector thay đổi.

```vue
<script setup>
import { ref, provide, readonly } from 'vue'

const count = ref(0)
provide('read-only-count', readonly(count))
</script>
```

</div>

<div class="options-api">

Để injection được liên kết phản ứng với provider, ta cần provide một thuộc tính computed bằng hàm [computed()](/api/reactivity-core#computed):

```js{12}
import { computed } from 'vue'

export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    return {
      // provide tường minh một thuộc tính computed
      message: computed(() => this.message)
    }
  }
}
```

[Ví dụ đầy đủ về provide + inject có tính phản ứng](https://play.vuejs.org/#eNqNUctqwzAQ/JVFFyeQxnfjBEoPPfULqh6EtYlV9EKWTcH43ytZtmPTQA0CsdqZ2dlRT16tPXctkoKUTeWE9VeqhbLGeXirheRwc0ZBds7HKkKzBdBDZZRtPXIYJlzqU40/I4LjjbUyIKmGEWw0at8UgZrUh1PscObZ4ZhQAA596/RcAShsGnbHArIapTRBP74O8Up060wnOO5QmP0eAvZyBV+L5jw1j2tZqsMp8yWRUHhUVjKPoQIohQ460L0ow1FeKJlEKEnttFweijJfiORElhCf5f3umObb0B9PU/I7kk17PJj7FloN/2t7a2Pj/Zkdob+x8gV8ZlMs2de/8+14AXwkBngD9zgVqjg2rNXPvwjD+EdlHilrn8MvtvD1+Q==)

Hàm `computed()` thường được dùng trong các component Composition API, nhưng cũng có thể được dùng để bổ trợ cho một số trường hợp nhất định trong Options API. Bạn có thể tìm hiểu thêm cách dùng của nó bằng cách đọc [Nền tảng về tính phản ứng](/guide/essentials/reactivity-fundamentals) và [Thuộc tính computed](/guide/essentials/computed) với API Preference đặt ở Composition API.

</div>

## Làm Việc Với Symbol Key {#working-with-symbol-keys}

Cho đến nay, trong các ví dụ chúng ta vẫn đang dùng injection key dạng chuỗi. Nếu bạn đang làm việc trong một ứng dụng lớn có nhiều dependency provider, hoặc đang viết component để người khác dùng, tốt nhất nên dùng injection key dạng [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) để tránh khả năng trùng tên.

Ta nên export các Symbol này trong một file riêng:

```js [keys.js]
export const myInjectionKey = Symbol()
```

<div class="composition-api">

```js
// trong component provider
import { provide } from 'vue'
import { myInjectionKey } from './keys.js'

provide(myInjectionKey, {
  /* dữ liệu cần provide */
})
```

```js
// trong component injector
import { inject } from 'vue'
import { myInjectionKey } from './keys.js'

const injected = inject(myInjectionKey)
```

Xem thêm: [Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

</div>

<div class="options-api">

```js
// trong component provider
import { myInjectionKey } from './keys.js'

export default {
  provide() {
    return {
      [myInjectionKey]: {
        /* dữ liệu cần provide */
      }
    }
  }
}
```

```js
// trong component injector
import { myInjectionKey } from './keys.js'

export default {
  inject: {
    injected: { from: myInjectionKey }
  }
}
```

</div>
