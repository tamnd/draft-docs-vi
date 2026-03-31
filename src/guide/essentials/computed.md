# Computed Properties {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Free Vue.js Computed Properties Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Free Vue.js Computed Properties Lesson"/>
</div>

## Ví dụ cơ bản {#basic-example}

Các biểu thức trong template rất tiện, nhưng chỉ phù hợp cho các thao tác đơn giản. Nếu đưa quá nhiều logic vào template, nó sẽ trở nên cồng kềnh và khó bảo trì. Ví dụ, nếu chúng ta có một object với một array lồng bên trong:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

</div>

Và chúng ta muốn hiển thị thông báo khác nhau tùy theo việc `author` có sách hay không:

```vue-html
<p>Đã xuất bản sách:</p>
<span>{{ author.books.length > 0 ? 'Có' : 'Không' }}</span>
```

Lúc này, template bắt đầu trở nên khó đọc. Chúng ta phải nhìn kỹ mới hiểu nó đang tính toán dựa trên `author.books`. Quan trọng hơn, chúng ta không muốn lặp lại logic này nhiều lần.

Vì vậy, với logic phức tạp liên quan đến reactive data, nên dùng **computed property**. Ví dụ trên viết lại như sau:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // computed getter
    publishedBooksMessage() {
      // `this` trỏ tới instance của component
      return this.author.books.length > 0 ? 'Có' : 'Không'
    }
  }
}
```

```vue-html
<p>Đã xuất bản sách:</p>
<span>{{ publishedBooksMessage }}</span>
```

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Có' : 'Không'
})
</script>

<template>
  <p>Đã xuất bản sách:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

</div>

Ở đây chúng ta khai báo computed property `publishedBooksMessage`.

Khi thay đổi giá trị của `books`, `publishedBooksMessage` sẽ tự động cập nhật.

Bạn có thể bind computed giống như property bình thường. Vue biết rằng `publishedBooksMessage` phụ thuộc vào `author.books`, nên sẽ cập nhật khi dữ liệu thay đổi.

> Xem thêm: [Typing Computed Properties](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

`computed()` nhận vào một getter function, và trả về một **computed ref**. Giống ref thông thường, bạn có thể truy cập bằng `.value`. Tuy nhiên trong template, nó được unwrap tự động nên không cần `.value`.

Computed property tự động theo dõi các dependency reactive. Khi `author.books` thay đổi, giá trị sẽ được tính lại.

> Xem thêm: [Typing Computed](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

</div>

## Computed caching vs Methods {#computed-caching-vs-methods}

Bạn có thể đạt cùng kết quả bằng cách gọi method:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Có' : 'Không'
  }
}
```

</div>

<div class="composition-api">

```js
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Có' : 'Không'
}
```

</div>

Sự khác biệt là **computed property được cache dựa trên dependency reactive**. Nó chỉ tính lại khi dependency thay đổi. Nếu `author.books` không đổi, truy cập nhiều lần sẽ trả về giá trị đã cache.

Ví dụ sau sẽ không bao giờ cập nhật vì `Date.now()` không phải reactive:

<div class="options-api">

```js
computed: {
  now() {
    return Date.now()
  }
}
```

</div>

<div class="composition-api">

```js
const now = computed(() => Date.now())
```

</div>

Ngược lại, method luôn chạy lại mỗi lần render.

Tại sao chúng ta cần caching? Hãy tưởng tượng chúng ta có một computed property tốn kém là `list`, cần lặp qua một array khổng lồ và thực hiện nhiều tính toán. Rồi chúng ta có thể có các computed property khác phụ thuộc vào `list`. Nếu không có caching, chúng ta sẽ phải thực thi getter của `list` nhiều hơn mức cần thiết! Trong trường hợp không muốn dùng caching, hãy dùng method call thay thế.

## Writable Computed {#writable-computed}

Mặc định computed chỉ có getter. Nếu gán giá trị sẽ bị warning. Nếu cần computed có thể ghi, cung cấp cả getter và setter:

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      get() {
        return this.firstName + ' ' + this.lastName
      },
      set(newValue) {
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Khi chạy `this.fullName = 'John Doe'`, setter sẽ được gọi và `this.firstName` cùng `this.lastName` sẽ được cập nhật tương ứng.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  get() {
    return firstName.value + ' ' + lastName.value
  },
  set(newValue) {
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Khi chạy `fullName.value = 'John Doe'`, setter sẽ được gọi và `firstName` cùng `lastName` sẽ được cập nhật tương ứng.

</div>

## Lấy giá trị trước đó {#previous}

* Chỉ hỗ trợ từ 3.4+

<p class="options-api">
Nếu cần, bạn có thể lấy giá trị trước đó được trả về bởi computed property bằng cách truy cập tham số thứ hai của getter:
</p>

<p class="composition-api">
Nếu cần, bạn có thể lấy giá trị trước đó được trả về bởi computed property bằng cách truy cập tham số đầu tiên của getter:
</p>

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 2
    }
  },
  computed: {
    alwaysSmall(_, previous) {
      if (this.count <= 3) {
        return this.count
      }
      return previous
    }
  }
}
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value
  }
  return previous
})
</script>
```

</div>

Trong trường hợp bạn dùng writable computed:

<div class="options-api">

```js
computed: {
  alwaysSmall: {
    get(_, previous) {
      if (this.count <= 3) {
        return this.count
      }
      return previous
    },
    set(newValue) {
      this.count = newValue * 2
    }
  }
}
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

const alwaysSmall = computed({
  get(previous) {
    if (count.value <= 3) {
      return count.value
    }
    return previous
  },
  set(newValue) {
    count.value = newValue * 2
  }
})
</script>
```

</div>

## Best Practices {#best-practices}

### Getter không nên có side effects {#getters-should-be-side-effect-free}

Điều quan trọng cần ghi nhớ là computed getter function chỉ nên thực hiện tính toán thuần túy và không được có side effect. Ví dụ, **đừng mutate state khác, thực hiện request async, hay thao tác DOM bên trong computed getter!** Hãy coi computed property như mô tả cách tính toán một giá trị từ state khác — trách nhiệm duy nhất của nó là tính toán và trả về giá trị đó. Ở phần sau trong hướng dẫn, chúng ta sẽ thảo luận về cách thực hiện side effect để phản ứng với thay đổi state bằng [watchers](./watchers).

### Không mutate giá trị computed {#avoid-mutating-computed-value}

Giá trị trả về từ computed là trạng thái dẫn xuất (derived state). Nó giống snapshot tạm thời. Không nên mutate trực tiếp. Thay vào đó, cập nhật state gốc để trigger tính toán lại.
