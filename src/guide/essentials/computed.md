# Thuộc Tính Computed {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Free Vue.js Computed Properties Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Free Vue.js Computed Properties Lesson"/>
</div>

## Ví Dụ Cơ Bản {#basic-example}

Các biểu thức trong template rất tiện lợi, nhưng chúng chỉ phù hợp cho những thao tác đơn giản. Đặt quá nhiều logic vào template sẽ khiến nó trở nên cồng kềnh và khó bảo trì. Ví dụ, nếu chúng ta có một đối tượng chứa một mảng lồng nhau:

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

Và chúng ta muốn hiển thị các thông báo khác nhau tùy thuộc vào việc `author` đã có sách nào chưa:

```vue-html
<p>Has published books:</p>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
```

Lúc này template đã bắt đầu trở nên lộn xộn. Chúng ta phải nhìn một lúc mới nhận ra rằng nó đang thực hiện một phép tính dựa trên `author.books`. Quan trọng hơn, nếu cần dùng phép tính này nhiều lần trong template, chúng ta sẽ không muốn lặp đi lặp lại như vậy.

Đó là lý do tại sao đối với logic phức tạp có liên quan đến dữ liệu reactive, nên sử dụng **thuộc tính computed**. Đây là ví dụ tương tự sau khi tái cấu trúc:

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
    // a computed getter
    publishedBooksMessage() {
      // `this` points to the component instance
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

```vue-html
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```

[Thử trên Playground](https://play.vuejs.org/#eNqFkN1KxDAQhV/l0JsqaFfUq1IquwiKsF6JINaLbDNui20S8rO4lL676c82eCFCIDOZMzkzXxetlUoOjqI0ykypa2XzQtC3ktqC0ydzjUVXCIAzy87OpxjQZJ0WpwxgzlZSp+EBEKylFPGTrATuJcUXobST8sukeA8vQPzqCNe4xJofmCiJ48HV/FfbLLrxog0zdfmn4tYrXirC9mgs6WMcBB+nsJ+C8erHH0rZKmeJL0sot2tqUxHfDONuyRi2p4BggWCr2iQTgGTcLGlI7G2FHFe4Q/xGJoYn8SznQSbTQviTrRboPrHUqoZZ8hmQqfyRmTDFTC1bqalsFBN5183o/3NG33uvoWUwXYyi/gdTEpwK)

Ở đây chúng ta đã khai báo một thuộc tính computed là `publishedBooksMessage`.

Hãy thử thay đổi giá trị của mảng `books` trong `data` của ứng dụng, bạn sẽ thấy `publishedBooksMessage` thay đổi theo.

Bạn có thể ràng buộc dữ liệu với thuộc tính computed trong template giống như một thuộc tính thông thường. Vue biết rằng `this.publishedBooksMessage` phụ thuộc vào `this.author.books`, vì vậy khi `this.author.books` thay đổi, nó sẽ cập nhật tất cả các ràng buộc phụ thuộc vào `this.publishedBooksMessage`.

Xem thêm: [Typing Computed Properties](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

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

// a computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp1kE9Lw0AQxb/KI5dtoTainkoaaREUoZ5EEONhm0ybYLO77J9CCfnuzta0vdjbzr6Zeb95XbIwZroPlMySzJW2MR6OfDB5oZrWaOvRwZIsfbOnCUrdmuCpQo+N1S0ET4pCFarUynnI4GttMT9PjLpCAUq2NIN41bXCkyYxiZ9rrX/cDF/xDYiPQLjDDRbVXqqSHZ5DUw2tg3zP8lK6pvxHe2DtvSasDs6TPTAT8F2ofhzh0hTygm5pc+I1Yb1rXE3VMsKsyDm5JcY/9Y5GY8xzHI+wnIpVw4nTI/10R2rra+S4xSPEJzkBvvNNs310ztK/RDlLLjy1Zic9cQVkJn+R7gIwxJGlMXiWnZEq77orhH3Pq2NH9DjvTfpfSBSbmA==)

Ở đây chúng ta đã khai báo một thuộc tính computed là `publishedBooksMessage`. Hàm `computed()` nhận vào một [hàm getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), và giá trị trả về là một **computed ref**. Tương tự như các ref thông thường, bạn có thể truy cập kết quả computed qua `publishedBooksMessage.value`. Computed ref cũng được tự động unwrap trong template nên bạn có thể tham chiếu đến chúng mà không cần `.value` trong các biểu thức template.

Thuộc tính computed tự động theo dõi các phụ thuộc reactive của nó. Vue biết rằng phép tính của `publishedBooksMessage` phụ thuộc vào `author.books`, vì vậy khi `author.books` thay đổi, nó sẽ cập nhật tất cả các ràng buộc phụ thuộc vào `publishedBooksMessage`.

Xem thêm: [Typing Computed](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

</div>

## Computed Caching và Methods {#computed-caching-vs-methods}

Bạn có thể nhận thấy rằng chúng ta có thể đạt được kết quả tương tự bằng cách gọi một method trong biểu thức:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// in component
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Yes' : 'No'
  }
}
```

</div>

<div class="composition-api">

```js
// in component
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```

</div>

Thay vì dùng thuộc tính computed, chúng ta có thể định nghĩa hàm tương tự như một method. Về kết quả cuối cùng, hai cách tiếp cận này thực sự hoàn toàn giống nhau. Tuy nhiên, điểm khác biệt là **thuộc tính computed được cache dựa trên các phụ thuộc reactive của chúng.** Một thuộc tính computed chỉ được tính lại khi một trong những phụ thuộc reactive của nó thay đổi. Điều này có nghĩa là miễn là `author.books` chưa thay đổi, nhiều lần truy cập `publishedBooksMessage` sẽ trả về ngay lập tức kết quả đã tính trước đó mà không cần chạy lại hàm getter.

Điều này cũng có nghĩa là thuộc tính computed sau đây sẽ không bao giờ được cập nhật, vì `Date.now()` không phải là một phụ thuộc reactive:

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

Ngược lại, mỗi khi render lại xảy ra, một lời gọi method sẽ **luôn luôn** thực thi hàm đó.

Tại sao chúng ta cần cache? Hãy tưởng tượng chúng ta có một thuộc tính computed tốn nhiều tài nguyên là `list`, cần duyệt qua một mảng lớn và thực hiện nhiều phép tính. Sau đó, chúng ta có thể có các thuộc tính computed khác phụ thuộc vào `list`. Nếu không có cache, chúng ta sẽ phải thực thi getter của `list` nhiều lần hơn mức cần thiết! Trong trường hợp không muốn dùng cache, hãy sử dụng lời gọi method thay thế.

## Computed Có Thể Ghi {#writable-computed}

Mặc định, thuộc tính computed chỉ có getter. Nếu bạn cố gán giá trị mới cho một thuộc tính computed, bạn sẽ nhận được cảnh báo lúc chạy. Trong những trường hợp hiếm gặp khi bạn cần một thuộc tính computed "có thể ghi", bạn có thể tạo ra nó bằng cách cung cấp cả getter lẫn setter:

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
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // Note: we are using destructuring assignment syntax here.
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Bây giờ khi bạn chạy `this.fullName = 'John Doe'`, setter sẽ được gọi và `this.firstName` cùng `this.lastName` sẽ được cập nhật tương ứng.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Note: we are using destructuring assignment syntax here.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Bây giờ khi bạn chạy `fullName.value = 'John Doe'`, setter sẽ được gọi và `firstName` cùng `lastName` sẽ được cập nhật tương ứng.

</div>

## Lấy Giá Trị Trước Đó {#previous}

- Chỉ hỗ trợ từ phiên bản 3.4+

<p class="options-api">
Trong trường hợp cần thiết, bạn có thể lấy giá trị trước đó mà thuộc tính computed trả về bằng cách truy cập tham số thứ hai của getter:
</p>

<p class="composition-api">
Trong trường hợp cần thiết, bạn có thể lấy giá trị trước đó mà thuộc tính computed trả về bằng cách truy cập tham số đầu tiên của getter:
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
    // This computed will return the value of count when it's less or equal to 3.
    // When count is >=4, the last value that fulfilled our condition will be returned
    // instead until count is less or equal to 3
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

// This computed will return the value of count when it's less or equal to 3.
// When count is >=4, the last value that fulfilled our condition will be returned
// instead until count is less or equal to 3
const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value
  }

  return previous
})
</script>
```
</div>

Trong trường hợp bạn dùng computed có thể ghi:

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


## Thực Hành Tốt Nhất {#best-practices}

### Getter không nên có side effect {#getters-should-be-side-effect-free}

Cần nhớ rằng các hàm getter của computed chỉ nên thực hiện tính toán thuần túy và không có side effect. Ví dụ, **không được thay đổi state khác, thực hiện các yêu cầu bất đồng bộ, hay thao tác với DOM bên trong một computed getter!** Hãy nghĩ về thuộc tính computed như là việc mô tả một cách khai báo cách dẫn xuất một giá trị từ các giá trị khác — trách nhiệm duy nhất của nó là tính toán và trả về giá trị đó. Ở phần sau của hướng dẫn, chúng ta sẽ thảo luận về cách thực hiện side effect khi state thay đổi bằng [watchers](./watchers).

### Tránh thay đổi giá trị computed {#avoid-mutating-computed-value}

Giá trị trả về từ thuộc tính computed là state dẫn xuất. Hãy nghĩ về nó như một bản chụp nhanh — mỗi khi state nguồn thay đổi, một bản chụp nhanh mới được tạo ra. Việc thay đổi một bản chụp nhanh là không hợp lý, vì vậy giá trị trả về từ computed nên được coi là chỉ đọc và không bao giờ bị thay đổi trực tiếp — thay vào đó, hãy cập nhật state nguồn mà nó phụ thuộc vào để kích hoạt các phép tính mới.
