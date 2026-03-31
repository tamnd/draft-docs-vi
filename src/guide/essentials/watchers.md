# Watchers {#watchers}

## Ví dụ cơ bản {#basic-example}

Thuộc tính computed cho phép chúng ta khai báo các giá trị dẫn xuất một cách rõ ràng. Tuy nhiên, có những trường hợp chúng ta cần thực hiện các "side effect" khi state thay đổi — ví dụ như thay đổi DOM, hoặc cập nhật một phần state khác dựa trên kết quả của một async operation.

<div class="options-api">

Với Options API, chúng ta có thể dùng [tùy chọn `watch`](/api/options-state#watch) để kích hoạt một hàm bất cứ khi nào một thuộc tính phản ứng thay đổi:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;-)',
      loading: false
    }
  },
  watch: {
    // whenever question changes, this function will run
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.loading = true
      this.answer = 'Thinking...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Error! Could not reach the API. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  Ask a yes/no question:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[Thử trong Playground](https://play.vuejs.org/#eNp9VE1v2zAM/SucLnaw1D70lqUbsiKH7rB1W4++aDYdq5ElTx9xgiD/fbT8lXZFAQO2+Mgn8pH0mW2aJjl4ZCu2trkRjfucKTw22jgosOReOjhnCqDgjseL/hvAoPNGjSeAvx6tE1qtIIqWo5Er26Ih088BteCt51KeINfKcaGAT5FQc7NP4NPNYiaQmhdC7VZQcmlxMF+61yUcWu7yajVmkabQVqjwgGZmzSuudmiX4CphofQqD+ZWSAnGqz5y9I4VtmOuS9CyGA9T3QCihGu3RKhc+gJtHH2JFld+EG5Mdug2QYZ4MSKhgBd11OgqXdipEm5PKoer0Jk2kA66wB044/EF1GtOSPRUCbUnryRJosnFnK4zpC5YR7205M9bLhyUSIrGUeVcY1dpekKrdNK6MuWNiKYKXt8V98FElDxbknGxGLCpZMi7VkGMxmjzv0pz1tvO4QPcay8LULoj5RToKoTN40MCEXyEQDJTl0KFmXpNOqsUxudN+TNFzzqdJp8ODutGcod0Alg34QWwsXsaVtIjVXqe9h5bC9V4B4ebWhco7zI24hmDVSEs/yOxIPOQEFnTnjzt2emS83nYFrhcevM6nRJhS+Ys9aoUu6Av7WqoNWO5rhsh0fxownplbBqhjJEmuv0WbN2UDNtDMRXm+zfsz/bY2TL2SH1Ec8CMTZjjhqaxh7e/v+ORvieQqvaSvN8Bf6HV0veSdG5fvSoo7Su/kO1D3f13SKInuz06VHYsahzzfl0yRj+s+3dKn9O9TW7HPrPLP624lFU=)

Tùy chọn `watch` cũng hỗ trợ đường dẫn dạng dot-delimited làm key:

```js
export default {
  watch: {
    // Note: only simple paths. Expressions are not supported.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Với Composition API, chúng ta có thể dùng [hàm `watch`](/api/reactivity-core#watch) để kích hoạt một callback bất cứ khi nào một phần state phản ứng thay đổi:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')
const loading = ref(false)

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

### Các kiểu nguồn của Watch {#watch-source-types}

Tham số đầu tiên của `watch` có thể là nhiều kiểu "nguồn" phản ứng khác nhau: có thể là một ref (kể cả computed ref), một reactive object, một [hàm getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), hoặc một mảng gồm nhiều nguồn:

```js
const x = ref(0)
const y = ref(0)

// single ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// array of multiple sources
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

Lưu ý rằng bạn không thể watch trực tiếp một thuộc tính của reactive object theo cách này:

```js
const obj = reactive({ count: 0 })

// this won't work because we are passing a number to watch()
watch(obj.count, (count) => {
  console.log(`Count is: ${count}`)
})
```

Thay vào đó, hãy dùng getter:

```js
// instead, use a getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`Count is: ${count}`)
  }
)
```

</div>

## Deep Watchers {#deep-watchers}

<div class="options-api">

`watch` mặc định là shallow: callback chỉ kích hoạt khi thuộc tính được watch được gán giá trị mới — nó sẽ không kích hoạt khi các thuộc tính lồng nhau bên trong thay đổi. Nếu bạn muốn callback chạy với mọi thay đổi lồng nhau, hãy dùng deep watcher:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Note: `newValue` will be equal to `oldValue` here
        // on nested mutations as long as the object itself
        // hasn't been replaced.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Khi bạn gọi `watch()` trực tiếp trên một reactive object, nó sẽ ngầm tạo ra một deep watcher — callback sẽ được kích hoạt với mọi thay đổi lồng nhau:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // fires on nested property mutations
  // Note: `newValue` will be equal to `oldValue` here
  // because they both point to the same object!
})

obj.count++
```

Cần phân biệt trường hợp này với getter trả về một reactive object — trong trường hợp sau, callback chỉ kích hoạt nếu getter trả về một object khác:

```js
watch(
  () => state.someObject,
  () => {
    // fires only when state.someObject is replaced
  }
)
```

Tuy nhiên, bạn có thể ép trường hợp thứ hai trở thành deep watcher bằng cách dùng tùy chọn `deep` tường minh:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Note: `newValue` will be equal to `oldValue` here
    // *unless* state.someObject has been replaced
  },
  { deep: true }
)
```

</div>

Từ Vue 3.5+, tùy chọn `deep` cũng có thể nhận một số nguyên để chỉ định độ sâu duyệt tối đa — tức là Vue sẽ duyệt bao nhiêu cấp lồng nhau trong object được watch.

:::warning Sử dụng cẩn thận
Deep watcher yêu cầu duyệt qua tất cả các thuộc tính lồng nhau trong object được watch, và có thể tốn kém khi dùng với cấu trúc dữ liệu lớn. Chỉ dùng khi thực sự cần thiết và chú ý đến ảnh hưởng hiệu năng.
:::

## Eager Watchers {#eager-watchers}

Mặc định, `watch` là lazy: callback sẽ không được gọi cho đến khi nguồn được watch thay đổi. Tuy nhiên trong một số trường hợp, chúng ta muốn cùng logic callback đó chạy ngay lập tức — ví dụ, khi muốn tải dữ liệu khởi tạo, rồi tải lại mỗi khi state liên quan thay đổi.

<div class="options-api">

Chúng ta có thể ép callback của watcher chạy ngay lập tức bằng cách khai báo nó dưới dạng object có hàm `handler` và tùy chọn `immediate: true`:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // this will be run immediately on component creation.
      },
      // force eager callback execution
      immediate: true
    }
  }
  // ...
}
```

Lần chạy đầu tiên của hàm handler sẽ xảy ra ngay trước hook `created`. Vue đã xử lý xong các tùy chọn `data`, `computed` và `methods`, nên các thuộc tính đó sẽ có sẵn trong lần gọi đầu tiên.

</div>

<div class="composition-api">

Chúng ta có thể ép callback của watcher chạy ngay lập tức bằng cách truyền tùy chọn `immediate: true`:

```js
watch(
  source,
  (newValue, oldValue) => {
    // executed immediately, then again when `source` changes
  },
  { immediate: true }
)
```

</div>

## Once Watchers {#once-watchers}

- Chỉ hỗ trợ từ phiên bản 3.4+

Callback của watcher sẽ thực thi mỗi khi nguồn được watch thay đổi. Nếu bạn muốn callback chỉ kích hoạt một lần duy nhất khi nguồn thay đổi, hãy dùng tùy chọn `once: true`.

<div class="options-api">

```js
export default {
  watch: {
    source: {
      handler(newValue, oldValue) {
        // when `source` changes, triggers only once
      },
      once: true
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(
  source,
  (newValue, oldValue) => {
    // when `source` changes, triggers only once
  },
  { once: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Rất phổ biến khi callback của watcher dùng chính xác cùng state phản ứng như nguồn. Ví dụ, hãy xem đoạn code sau — dùng watcher để tải một tài nguyên từ xa mỗi khi ref `todoId` thay đổi:

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

Đặc biệt, chú ý cách watcher dùng `todoId` hai lần: một lần làm nguồn và một lần nữa bên trong callback.

Có thể rút gọn điều này với [`watchEffect()`](/api/reactivity-core#watcheffect). `watchEffect()` cho phép chúng ta tự động theo dõi các dependency phản ứng của callback. Watcher trên có thể được viết lại như sau:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Ở đây, callback sẽ chạy ngay lập tức, không cần chỉ định `immediate: true`. Trong quá trình thực thi, nó sẽ tự động theo dõi `todoId.value` như một dependency (tương tự như thuộc tính computed). Mỗi khi `todoId.value` thay đổi, callback sẽ được chạy lại. Với `watchEffect()`, chúng ta không còn cần truyền `todoId` tường minh làm giá trị nguồn nữa.

Bạn có thể xem [ví dụ này](/examples/#fetching-data) về `watchEffect()` và việc lấy dữ liệu phản ứng trong thực tế.

Với các ví dụ như trên, chỉ có một dependency, lợi ích của `watchEffect()` là tương đối nhỏ. Nhưng với những watcher có nhiều dependency, dùng `watchEffect()` sẽ loại bỏ gánh nặng phải tự duy trì danh sách dependency. Ngoài ra, nếu bạn cần watch nhiều thuộc tính trong một cấu trúc dữ liệu lồng nhau, `watchEffect()` có thể hiệu quả hơn deep watcher, vì nó chỉ theo dõi những thuộc tính thực sự được dùng trong callback, thay vì duyệt đệ quy tất cả.

:::tip
`watchEffect` chỉ theo dõi các dependency trong quá trình thực thi **đồng bộ**. Khi dùng với async callback, chỉ những thuộc tính được truy cập trước lần `await` đầu tiên mới được theo dõi.
:::

### `watch` so với `watchEffect` {#watch-vs-watcheffect}

`watch` và `watchEffect` đều cho phép thực hiện side effect một cách phản ứng. Sự khác biệt chính là cách chúng theo dõi các dependency phản ứng:

- `watch` chỉ theo dõi nguồn được chỉ định tường minh. Nó không theo dõi bất cứ thứ gì được truy cập bên trong callback. Ngoài ra, callback chỉ kích hoạt khi nguồn thực sự thay đổi. `watch` tách biệt việc theo dõi dependency khỏi side effect, cho chúng ta kiểm soát chính xác hơn về thời điểm callback nên kích hoạt.

- `watchEffect`, ngược lại, kết hợp việc theo dõi dependency và side effect vào một giai đoạn. Nó tự động theo dõi mọi thuộc tính phản ứng được truy cập trong quá trình thực thi đồng bộ. Cách này tiện lợi hơn và thường cho ra code ngắn gọn hơn, nhưng làm cho các dependency phản ứng kém tường minh hơn.

</div>

## Dọn dẹp Side Effect {#side-effect-cleanup}

Đôi khi chúng ta có thể thực hiện side effect, chẳng hạn như gửi async request, trong một watcher:

<div class="composition-api">

```js
watch(id, (newId) => {
  fetch(`/api/${newId}`).then(() => {
    // callback logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId) {
      fetch(`/api/${newId}`).then(() => {
        // callback logic
      })
    }
  }
}
```

</div>

Nhưng điều gì xảy ra nếu `id` thay đổi trước khi request hoàn thành? Khi request trước hoàn thành, nó vẫn sẽ kích hoạt callback với một giá trị ID đã lỗi thời. Lý tưởng nhất, chúng ta muốn hủy request cũ khi `id` thay đổi sang giá trị mới.

Chúng ta có thể dùng API [`onWatcherCleanup()`](/api/reactivity-core#onwatchercleanup) <sup class="vt-badge" data-text="3.5+" /> để đăng ký một hàm dọn dẹp sẽ được gọi khi watcher bị vô hiệu và chuẩn bị chạy lại:

<div class="composition-api">

```js {10-13}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()

  fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
    // callback logic
  })

  onWatcherCleanup(() => {
    // abort stale request
    controller.abort()
  })
})
```

</div>
<div class="options-api">

```js {12-15}
import { onWatcherCleanup } from 'vue'

export default {
  watch: {
    id(newId) {
      const controller = new AbortController()

      fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
        // callback logic
      })

      onWatcherCleanup(() => {
        // abort stale request
        controller.abort()
      })
    }
  }
}
```

</div>

Lưu ý rằng `onWatcherCleanup` chỉ được hỗ trợ từ Vue 3.5+ và phải được gọi trong quá trình thực thi đồng bộ của effect function của `watchEffect` hoặc hàm callback của `watch`: bạn không thể gọi nó sau câu lệnh `await` trong một async function.

Ngoài ra, một hàm `onCleanup` cũng được truyền vào callback của watcher như tham số thứ 3<span class="composition-api">, và vào effect function của `watchEffect` như tham số đầu tiên</span>:

<div class="composition-api">

```js
watch(id, (newId, oldId, onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})

watchEffect((onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId, oldId, onCleanup) {
      // ...
      onCleanup(() => {
        // cleanup logic
      })
    }
  }
}
```

</div>

`onCleanup` được truyền qua tham số hàm được gắn với instance của watcher, nên nó không bị ràng buộc bởi giới hạn đồng bộ như `onWatcherCleanup`.

## Thời điểm Flush của Callback {#callback-flush-timing}

Khi bạn thay đổi state phản ứng, nó có thể kích hoạt cả các cập nhật component Vue và các callback của watcher mà bạn tạo.

Tương tự như cập nhật component, các callback watcher do người dùng tạo được gom lại để tránh gọi trùng lặp. Ví dụ, chúng ta không muốn watcher kích hoạt hàng nghìn lần nếu ta đồng bộ đẩy hàng nghìn phần tử vào một mảng đang được watch.

Mặc định, callback của watcher được gọi **sau** khi component cha cập nhật (nếu có), và **trước** khi DOM của component chủ cập nhật. Điều này có nghĩa là nếu bạn cố truy cập DOM của component chủ bên trong callback watcher, DOM sẽ ở trạng thái chưa được cập nhật.

### Post Watchers {#post-watchers}

Nếu bạn muốn truy cập DOM của component chủ trong callback watcher **sau khi** Vue đã cập nhật nó, bạn cần chỉ định tùy chọn `flush: 'post'`:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

`watchEffect()` với flush post cũng có alias tiện lợi là `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* executed after Vue updates */
})
```

</div>

### Sync Watchers {#sync-watchers}

Bạn cũng có thể tạo watcher kích hoạt đồng bộ, trước mọi cập nhật do Vue quản lý:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'sync'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'sync'
})

watchEffect(callback, {
  flush: 'sync'
})
```

Sync `watchEffect()` cũng có alias tiện lợi là `watchSyncEffect()`:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* executed synchronously upon reactive data change */
})
```

</div>

:::warning Sử dụng cẩn thận
Sync watcher không được gom lại và kích hoạt mỗi khi phát hiện một thay đổi phản ứng. Bạn có thể dùng chúng để watch các giá trị boolean đơn giản, nhưng tránh dùng với các nguồn dữ liệu có thể bị thay đổi đồng bộ nhiều lần, ví dụ như mảng.
:::

<div class="options-api">

## `this.$watch()` \* {#this-watch}

Bạn cũng có thể tạo watcher theo kiểu lập trình bằng [phương thức instance `$watch()`](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Cách này hữu ích khi bạn cần thiết lập watcher theo điều kiện, hoặc chỉ watch thứ gì đó khi người dùng tương tác. Nó cũng cho phép bạn dừng watcher sớm.

</div>

## Dừng một Watcher {#stopping-a-watcher}

<div class="options-api">

Các watcher khai báo bằng tùy chọn `watch` hoặc phương thức instance `$watch()` sẽ tự động dừng khi component chủ bị unmount, vì vậy trong hầu hết trường hợp bạn không cần lo về việc tự dừng watcher.

Trong trường hợp hiếm gặp khi bạn cần dừng watcher trước khi component chủ bị unmount, API `$watch()` trả về một hàm để làm điều đó:

```js
const unwatch = this.$watch('foo', callback)

// ...when the watcher is no longer needed:
unwatch()
```

</div>

<div class="composition-api">

Các watcher được khai báo đồng bộ bên trong `setup()` hoặc `<script setup>` được gắn với instance của component chủ, và sẽ tự động dừng khi component chủ bị unmount. Trong hầu hết trường hợp, bạn không cần lo về việc tự dừng watcher.

Điều quan trọng ở đây là watcher phải được tạo **đồng bộ**: nếu watcher được tạo trong một async callback, nó sẽ không được gắn với component chủ và phải được dừng thủ công để tránh rò rỉ bộ nhớ. Đây là ví dụ:

```vue
<script setup>
import { watchEffect } from 'vue'

// this one will be automatically stopped
watchEffect(() => {})

// ...this one will not!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Để dừng watcher thủ công, hãy dùng hàm handle được trả về. Cách này hoạt động với cả `watch` và `watchEffect`:

```js
const unwatch = watchEffect(() => {})

// ...later, when no longer needed
unwatch()
```

Lưu ý rằng sẽ rất ít trường hợp bạn cần tạo watcher bất đồng bộ, và nên ưu tiên tạo đồng bộ bất cứ khi nào có thể. Nếu bạn cần chờ dữ liệu async, bạn có thể đưa logic watch vào dạng có điều kiện:

```js
// data to be loaded asynchronously
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // do something when data is loaded
  }
})
```

</div>
