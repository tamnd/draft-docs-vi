# TypeScript với Options API {#typescript-with-options-api}

> Trang này giả sử bạn đã đọc qua phần tổng quan về [Dùng Vue với TypeScript](./overview).

:::tip
Mặc dù Vue có hỗ trợ TypeScript khi dùng Options API, nhưng nên dùng Vue với TypeScript qua Composition API vì cách này cho phép suy luận kiểu đơn giản hơn, hiệu quả hơn và mạnh mẽ hơn.
:::

## Khai báo kiểu cho Props của Component {#typing-component-props}

Suy luận kiểu cho props trong Options API yêu cầu bọc component bằng `defineComponent()`. Nhờ đó, Vue có thể suy luận kiểu cho các prop dựa trên option `props`, có tính đến các option bổ sung như `required: true` và `default`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // bật suy luận kiểu
  props: {
    name: String,
    id: [Number, String],
    msg: { type: String, required: true },
    metadata: null
  },
  mounted() {
    this.name // kiểu: string | undefined
    this.id // kiểu: number | string | undefined
    this.msg // kiểu: string
    this.metadata // kiểu: any
  }
})
```

Tuy nhiên, option `props` ở runtime chỉ hỗ trợ dùng hàm constructor làm kiểu cho prop - không có cách nào để khai báo kiểu phức tạp như object có thuộc tính lồng nhau hoặc chữ ký lời gọi hàm.

Để chú thích kiểu props phức tạp, ta có thể dùng kiểu tiện ích `PropType`:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

interface Book {
  title: string
  author: string
  year: number
}

export default defineComponent({
  props: {
    book: {
      // khai báo kiểu cụ thể hơn thay vì `Object`
      type: Object as PropType<Book>,
      required: true
    },
    // có thể chú thích cả hàm
    callback: Function as PropType<(id: number) => void>
  },
  mounted() {
    this.book.title // string
    this.book.year // number

    // Lỗi TS: argument của kiểu 'string' không thể
    // gán cho parameter của kiểu 'number'
    this.callback?.('123')
  }
})
```

### Lưu ý {#caveats}

Nếu phiên bản TypeScript của bạn nhỏ hơn `4.7`, hãy cẩn thận khi dùng giá trị hàm cho các option `validator` và `default` của prop - hãy đảm bảo dùng arrow function:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

interface Book {
  title: string
  year?: number
}

export default defineComponent({
  props: {
    bookA: {
      type: Object as PropType<Book>,
      // Hãy chắc chắn dùng arrow function nếu phiên bản TypeScript nhỏ hơn 4.7
      default: () => ({
        title: 'Arrow Function Expression'
      }),
      validator: (book: Book) => !!book.title
    }
  }
})
```

Điều này ngăn TypeScript phải suy luận kiểu của `this` bên trong các hàm đó, điều mà không may có thể khiến suy luận kiểu thất bại. Đây là [hạn chế thiết kế](https://github.com/microsoft/TypeScript/issues/38845) trước đây, và đã được cải thiện trong [TypeScript 4.7](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-7.html#improved-function-inference-in-objects-and-methods).

## Khai báo kiểu cho Emits của Component {#typing-component-emits}

Ta có thể khai báo kiểu payload mong đợi cho một sự kiện được emit bằng cú pháp object của option `emits`. Ngoài ra, mọi sự kiện emit không được khai báo sẽ báo lỗi kiểu khi được gọi:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  emits: {
    addBook(payload: { bookName: string }) {
      // thực hiện kiểm tra lúc chạy
      return payload.bookName.length > 0
    }
  },
  methods: {
    onSubmit() {
      this.$emit('addBook', {
        bookName: 123 // Lỗi kiểu!
      })

      this.$emit('non-declared-event') // Lỗi kiểu!
    }
  }
})
```

## Khai báo kiểu cho Thuộc tính Computed {#typing-computed-properties}

Thuộc tính computed tự suy luận kiểu dựa trên giá trị trả về của nó:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    greeting() {
      return this.message + '!'
    }
  },
  mounted() {
    this.greeting // kiểu: string
  }
})
```

Trong một số trường hợp, bạn có thể muốn chú thích rõ ràng kiểu của thuộc tính computed để đảm bảo implementation đúng:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    // chú thích rõ ràng kiểu trả về
    greeting(): string {
      return this.message + '!'
    },

    // chú thích thuộc tính computed có thể ghi
    greetingUppercased: {
      get(): string {
        return this.greeting.toUpperCase()
      },
      set(newValue: string) {
        this.message = newValue.toUpperCase()
      }
    }
  }
})
```

Chú thích rõ ràng cũng có thể cần thiết trong một số trường hợp đặc biệt khi TypeScript không thể suy luận kiểu của thuộc tính computed do vòng lặp suy luận tròn.

## Khai báo kiểu cho Event Handler {#typing-event-handlers}

Khi xử lý sự kiện DOM native, đôi khi hữu ích khi khai báo kiểu đúng cho đối số truyền vào handler. Hãy xem ví dụ sau:

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  methods: {
    handleChange(event) {
      // `event` ngầm có kiểu `any`
      console.log(event.target.value)
    }
  }
})
</script>

<template>
  <input type="text" @change="handleChange" />
</template>
```

Không có chú thích kiểu, đối số `event` sẽ ngầm có kiểu `any`. Điều này cũng dẫn đến lỗi TS nếu `"strict": true` hoặc `"noImplicitAny": true` được dùng trong `tsconfig.json`. Do đó, nên chú thích rõ ràng đối số của event handler. Ngoài ra, bạn có thể cần dùng ép kiểu khi truy cập các thuộc tính của `event`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  methods: {
    handleChange(event: Event) {
      console.log((event.target as HTMLInputElement).value)
    }
  }
})
```

## Mở rộng Thuộc tính Toàn cục {#augmenting-global-properties}

Một số plugin cài đặt các thuộc tính có sẵn toàn cục cho tất cả instance component thông qua [`app.config.globalProperties`](/api/application#app-config-globalproperties). Ví dụ, ta có thể cài `this.$http` để lấy dữ liệu hoặc `this.$translate` để đa ngôn ngữ. Để tích hợp tốt với TypeScript, Vue cung cấp interface `ComponentCustomProperties` được thiết kế để mở rộng thông qua [module augmentation của TypeScript](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation):

```ts
import axios from 'axios'

declare module 'vue' {
  interface ComponentCustomProperties {
    $http: typeof axios
    $translate: (key: string) => string
  }
}
```

Xem thêm:

- [Unit test TypeScript cho type extensions của component](https://github.com/vuejs/core/blob/main/packages-private/dts-test/componentTypeExtensions.test-d.tsx)

### Vị trí đặt Type Augmentation {#type-augmentation-placement}

Ta có thể đặt type augmentation này trong một file `.ts`, hoặc trong một file `*.d.ts` áp dụng toàn dự án. Dù cách nào, hãy đảm bảo nó được đưa vào `tsconfig.json`. Với tác giả thư viện / plugin, file này nên được khai báo trong thuộc tính `types` của `package.json`.

Để tận dụng module augmentation, bạn cần đảm bảo augmentation được đặt trong một [TypeScript module](https://www.typescriptlang.org/docs/handbook/modules.html). Nghĩa là, file cần chứa ít nhất một `import` hoặc `export` ở cấp cao nhất, dù chỉ là `export {}`. Nếu augmentation được đặt ngoài module, nó sẽ ghi đè kiểu gốc thay vì mở rộng chúng!

```ts
// Không hoạt động, ghi đè kiểu gốc.
declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

```ts
// Hoạt động đúng
export {}

declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

## Mở rộng Custom Options {#augmenting-custom-options}

Một số plugin, ví dụ `vue-router`, hỗ trợ các option component tùy chỉnh như `beforeRouteEnter`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  beforeRouteEnter(to, from, next) {
    // ...
  }
})
```

Không có type augmentation phù hợp, các đối số của hook này sẽ ngầm có kiểu `any`. Ta có thể mở rộng interface `ComponentCustomOptions` để hỗ trợ các option tùy chỉnh này:

```ts
import { Route } from 'vue-router'

declare module 'vue' {
  interface ComponentCustomOptions {
    beforeRouteEnter?(to: Route, from: Route, next: () => void): void
  }
}
```

Giờ thì option `beforeRouteEnter` sẽ được khai báo kiểu đúng. Lưu ý đây chỉ là ví dụ - các thư viện có kiểu đầy đủ như `vue-router` nên tự thực hiện các augmentation này trong định nghĩa kiểu của chính chúng.

Vị trí của augmentation này phụ thuộc vào [các hạn chế giống nhau](#type-augmentation-placement) như augmentation thuộc tính toàn cục.

Xem thêm:

- [Unit test TypeScript cho type extensions của component](https://github.com/vuejs/core/blob/main/packages-private/dts-test/componentTypeExtensions.test-d.tsx)

## Khai báo kiểu cho Custom Directive Toàn cục {#typing-global-custom-directives}

Xem: [Khai báo kiểu cho Custom Directive Toàn cục](/guide/typescript/composition-api#typing-global-custom-directives) <sup class="vt-badge ts" />
