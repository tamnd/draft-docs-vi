# Thuộc tính computed {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Bài học miễn phí về thuộc tính computed trong Vue.js"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Bài học miễn phí về thuộc tính computed trong Vue.js"/>
</div>

## Ví dụ cơ bản {#basic-example}

Biểu thức trong template rất tiện, nhưng chúng chỉ phù hợp cho những thao tác đơn giản. Nếu nhồi quá nhiều logic vào template, mã sẽ trở nên rối và khó bảo trì. Ví dụ, giả sử chúng ta có một object chứa một mảng được lồng bên trong:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'Nguyen Van A',
        books: [
          'Vue 2 - Hướng dẫn nâng cao',
          'Vue 3 - Hướng dẫn cơ bản',
          'Vue 4 - Điều bí ẩn'
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
  name: 'Nguyen Van A',
  books: [
    'Vue 2 - Hướng dẫn nâng cao',
    'Vue 3 - Hướng dẫn cơ bản',
    'Vue 4 - Điều bí ẩn'
  ]
})
```

</div>

Và chúng ta muốn hiển thị thông báo khác nhau tùy theo `author` đã có sách xuất bản hay chưa:

```vue-html
<p>Đã xuất bản sách:</p>
<span>{{ author.books.length > 0 ? 'Có' : 'Không' }}</span>
```

Đến đây, template đã bắt đầu hơi rối. Ta phải nhìn kỹ một chút mới nhận ra nó đang thực hiện phép tính dựa trên `author.books`. Quan trọng hơn, chắc hẳn ta không muốn lặp lại đoạn tính toán này nhiều lần nếu cần dùng nó ở hơn một chỗ trong template.

Vì vậy, với logic phức tạp có liên quan đến dữ liệu phản ứng, bạn nên dùng **thuộc tính computed**. Dưới đây là chính ví dụ đó sau khi được viết lại:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'Nguyen Van A',
        books: [
          'Vue 2 - Hướng dẫn nâng cao',
          'Vue 3 - Hướng dẫn cơ bản',
          'Vue 4 - Điều bí ẩn'
        ]
      }
    }
  },
  computed: {
    // getter của computed
    publishedBooksMessage() {
      // `this` trỏ tới component instance
      return this.author.books.length > 0 ? 'Có' : 'Không'
    }
  }
}
```

```vue-html
<p>Đã xuất bản sách:</p>
<span>{{ publishedBooksMessage }}</span>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFkN1KxDAQhV/l0JsqaFfUq1IquwiKsF6JINaLbDNui20S8rO4lL676c82eCFCIDOZMzkzXxetlUoOjqI0ykypa2XzQtC3ktqC0ydzjUVXCIAzy87OpxjQZJ0WpwxgzlZSp+EBEKylFPGTrATuJcUXobST8sukeA8vQPzqCNe4xJofmCiJ48HV/FfbLLrxog0zdfmn4tYrXirC9mgs6WMcBB+nsJ+C8erHH0rZKmeJL0sot2tqUxHfDONuyRi2p4BggWCr2iQTgGTcLGlI7G2FHFe4Q/xGJoYn8SznQSbTQviTrRboPrHUqoZZ8hmQqfyRmTDFTC1bqalsFBN5183o/3NG33uvoWUwXYyi/gdTEpwK)

Ở đây, chúng ta đã khai báo một thuộc tính computed tên là `publishedBooksMessage`.

Hãy thử thay đổi giá trị của mảng `books` trong `data` của ứng dụng và bạn sẽ thấy `publishedBooksMessage` thay đổi theo.

Bạn có thể bind thuộc tính computed trong template giống như một property thông thường. Vue biết rằng `this.publishedBooksMessage` phụ thuộc vào `this.author.books`, nên nó sẽ cập nhật mọi binding đang phụ thuộc vào `this.publishedBooksMessage` khi `this.author.books` thay đổi.

Xem thêm: [Khai báo kiểu cho thuộc tính computed](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'Nguyen Van A',
  books: [
    'Vue 2 - Hướng dẫn nâng cao',
    'Vue 3 - Hướng dẫn cơ bản',
    'Vue 4 - Điều bí ẩn'
  ]
})

// một computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Có' : 'Không'
})
</script>

<template>
  <p>Đã xuất bản sách:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp1kE9Lw0AQxb/KI5dtoTainkoaaREUoZ5EEONhm0ybYLO77J9CCfnuzta0vdjbzr6Zeb95XbIwZroPlMySzJW2MR6OfDB5oZrWaOvRwZIsfbOnCUrdmuCpQo+N1S0ET4pCFarUynnI4GttMT9PjLpCAUq2NIN41bXCkyYxiZ9rrX/cDF/xDYiPQLjDDRbVXqqSHZ5DUw2tg3zP8lK6pvxHe2DtvSasDs6TPTAT8F2ofhzh0hTygm5pc+I1Yb1rXE3VMsKsyDm5JcY/9Y5GY8xzHI+wnIpVw4nTI/10R2rra+S4xSPEJzkBvvNNs310ztK/RDlLLjy1Zic9cQVkJn+R7gIwxJGlMXiWnZEq77orhH3Pq2NH9DjvTfpfSBSbmA==)

Ở đây, chúng ta đã khai báo một thuộc tính computed tên là `publishedBooksMessage`. Hàm `computed()` nhận vào một [hàm getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), và giá trị trả về là một **computed ref**. Tương tự như ref thông thường, bạn có thể truy cập kết quả computed qua `publishedBooksMessage.value`. Computed ref cũng sẽ được tự động mở bọc trong template, nên bạn có thể dùng nó trong biểu thức template mà không cần `.value`.

Một thuộc tính computed sẽ tự động theo dõi các dependency phản ứng của nó. Vue biết rằng phép tính của `publishedBooksMessage` phụ thuộc vào `author.books`, nên nó sẽ cập nhật mọi binding đang phụ thuộc vào `publishedBooksMessage` khi `author.books` thay đổi.

Xem thêm: [Khai báo kiểu cho computed](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

</div>

## Cache của computed và method {#computed-caching-vs-methods}

Có thể bạn đã nhận ra rằng ta cũng có thể đạt được kết quả tương tự bằng cách gọi một method trong biểu thức:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// trong component
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Có' : 'Không'
  }
}
```

</div>

<div class="composition-api">

```js
// trong component
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Có' : 'Không'
}
```

</div>

Thay vì dùng thuộc tính computed, ta có thể định nghĩa cùng logic đó dưới dạng method. Xét về kết quả cuối cùng thì hai cách này đúng là giống nhau. Tuy nhiên, điểm khác biệt nằm ở chỗ **thuộc tính computed được cache dựa trên các dependency phản ứng của nó.** Một thuộc tính computed chỉ được tính lại khi một trong các dependency phản ứng của nó thay đổi. Điều đó có nghĩa là miễn là `author.books` chưa thay đổi, việc truy cập `publishedBooksMessage` nhiều lần sẽ trả về ngay kết quả đã tính trước đó mà không cần chạy lại hàm getter.

Điều đó cũng có nghĩa là thuộc tính computed dưới đây sẽ không bao giờ cập nhật, vì `Date.now()` không phải dependency phản ứng:

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

Ngược lại, một lời gọi method sẽ **luôn** chạy lại hàm mỗi khi có re-render.

Tại sao lại cần cache? Hãy tưởng tượng bạn có một thuộc tính computed nặng tên là `list` — nó phải lặp qua một mảng rất lớn và thực hiện nhiều phép tính. Sau đó lại có những thuộc tính computed khác phụ thuộc vào `list`. Nếu không có cache, getter của `list` sẽ chạy lại nhiều lần không cần thiết. Trong những trường hợp bạn không muốn cache, hãy dùng method thay thế.

## Computed có thể ghi {#writable-computed}

Mặc định, thuộc tính computed chỉ có getter. Nếu bạn thử gán giá trị mới cho một thuộc tính computed, bạn sẽ nhận được cảnh báo lúc chạy. Trong một số ít trường hợp cần một computed "có thể ghi", bạn có thể tạo nó bằng cách cung cấp cả getter lẫn setter:

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'Van',
      lastName: 'Nguyen'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // Lưu ý: ở đây chúng ta dùng cú pháp gán destructuring.
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Bây giờ khi bạn chạy `this.fullName = 'Van Nguyen'`, setter sẽ được gọi và `this.firstName` cùng `this.lastName` sẽ được cập nhật tương ứng.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('Van')
const lastName = ref('Nguyen')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Lưu ý: ở đây chúng ta dùng cú pháp gán destructuring.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Bây giờ khi bạn chạy `fullName.value = 'Van Nguyen'`, setter sẽ được gọi và `firstName` cùng `lastName` sẽ được cập nhật tương ứng.

</div>

## Lấy giá trị trước đó {#previous}

- Chỉ được hỗ trợ từ 3.4+

<p class="options-api">
Nếu cần, bạn có thể lấy giá trị trước đó mà thuộc tính computed đã trả về
thông qua đối số thứ hai của getter:
</p>

<p class="composition-api">
Nếu cần, bạn có thể lấy giá trị trước đó mà thuộc tính computed đã trả về
thông qua đối số thứ nhất của getter:
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
    // Computed này sẽ trả về giá trị của count khi nó nhỏ hơn hoặc bằng 3.
    // Khi count >= 4, giá trị cuối cùng thỏa điều kiện sẽ được trả về
    // cho đến khi count quay về nhỏ hơn hoặc bằng 3
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

// Computed này sẽ trả về giá trị của count khi nó nhỏ hơn hoặc bằng 3.
// Khi count >= 4, giá trị cuối cùng thỏa điều kiện sẽ được trả về
// cho đến khi count quay về nhỏ hơn hoặc bằng 3
const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value
  }

  return previous
})
</script>
```
</div>

Nếu bạn đang dùng computed có thể ghi:

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 2
    }
  },
  computed: {
    alwaysSmall: {
      get(_, previous) {
        if (this.count <= 3) {
          return this.count
        }

        return previous;
      },
      set(newValue) {
        this.count = newValue * 2
      }
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


## Thực hành tốt nhất {#best-practices}

### Getter nên không có side effect {#getters-should-be-side-effect-free}

Điều quan trọng cần nhớ là hàm getter của computed chỉ nên thực hiện tính toán thuần túy và không tạo side effect. Ví dụ, **đừng thay đổi state khác, gửi request bất đồng bộ, hay sửa DOM bên trong getter của computed!** Hãy xem một thuộc tính computed như một khai báo mô tả cách lấy ra một giá trị dẫn xuất từ những giá trị khác. Nhiệm vụ duy nhất của nó là tính toán và trả về giá trị đó. Ở phần sau của hướng dẫn, chúng ta sẽ nói về cách thực hiện side effect khi state thay đổi bằng [watcher](./watchers).

### Tránh thay đổi giá trị computed {#avoid-mutating-computed-value}

Giá trị trả về từ một thuộc tính computed là state được suy ra. Hãy xem nó như một bản chụp nhanh (snapshot): mỗi khi state nguồn thay đổi, một bản chụp mới sẽ được tạo ra. Việc sửa một bản chụp như vậy không có nhiều ý nghĩa, nên giá trị computed cần được xem là chỉ đọc và không nên bị thay đổi trực tiếp. Thay vào đó, hãy cập nhật state nguồn mà nó đang phụ thuộc vào để kích hoạt tính toán lại.
