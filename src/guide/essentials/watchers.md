# Watchers {#watchers}

## Ví dụ cơ bản {#basic-example}

Computed properties cho phép chúng ta tính toán các giá trị dẫn xuất một cách khai báo. Tuy nhiên, có những trường hợp chúng ta cần thực hiện các "side effects" (tác dụng phụ) khi trạng thái thay đổi, ví dụ như thay đổi DOM, hoặc cập nhật một phần state khác dựa trên kết quả của một thao tác bất đồng bộ.

<div class="options-api">

Với Options API, chúng ta có thể dùng option [`watch`](/api/options-state#watch) để kích hoạt một function mỗi khi một property reactive thay đổi:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Câu hỏi thường chứa dấu hỏi. ;-)',
      loading: false
    }
  },
  watch: {
    // mỗi khi question thay đổi, function này sẽ chạy
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.loading = true
      this.answer = 'Đang suy nghĩ...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Lỗi! Không thể truy cập API. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  Hỏi một câu hỏi yes/no:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[Thử trong Playground](https://play.vuejs.org/#eNp9VE1v2zAM/SucLnaw1D70lqUbsiKH7rB1W4++aDYdq5ElTx9xgiD/fbT8lXZFAQO2+Mgn8pH0mW2aJjl4ZCu2trkRjfucKTw22jgosOReOjhnCqDgjseL/hvAoPNGjSeAvx6tE1qtIIqWo5Er26Ih088BteCt51KeINfKcaGAT5FQc7NP4NPNYiaQmhdC7VZQcmlxMF+61yUcWu7yajVmkabQVqjwgGZmzSuudmiX4CphofQqD+ZWSAnGqz5y9I4VtmOuS9CyGA9T3QCihGu3RKhc+gJtHH2JFld+EG5Mdug2QYZ4MSKhgBd11OgqXdipEm5PKoer0Jk2kA66wB044/EF1GtOSPRUCbUnryRJosnFnK4zpC5YR7205M9bLhyUSIrGUeVcY1dpekKrdNK6MuWNiKYKXt8V98FElDxbknGxGLCpZMi7VkGMxmjzv0pz1tvO4QPcay8LULoj5RToKoTN40MCEXyEQDJTl0KFmXpNOqsUxudN+TNFzzqdJp8ODutGcod0Alg34QWwsXsaVtIjVXqe9h5bC9V4B4ebWhco7zI24hmDVSEs/yOxIPOQEFnTnjzt2emS83nYFrhcevM6nRJhS+Ys9aoUu6Av7WqoNWO5rhsh0fxownplbBqhjJEmuv0WbN2UDNtDMRXm+zfsz/bY2TL2SH1Ec8CMTZjjhqaxh7e/v+ORvieQqvaSvN8Bf6HV0veSdG5fvSoo7Su/kO1D3f13SKInuz06VHYsahzzfl0yRj+s+3dKn9O9TW7HPrPLP624lFU=)

Option `watch` cũng hỗ trợ đường dẫn dạng dot làm key:

```js
export default {
  watch: {
    // Lưu ý: chỉ hỗ trợ đường dẫn đơn giản. Không hỗ trợ biểu thức.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Với Composition API, chúng ta có thể dùng function [`watch`](/api/reactivity-core#watch) để kích hoạt callback mỗi khi một phần state reactive thay đổi:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Câu hỏi thường chứa dấu hỏi. ;-)')
const loading = ref(false)

// watch hoạt động trực tiếp trên ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Đang suy nghĩ...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Lỗi! Không thể truy cập API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Hỏi một câu hỏi yes/no:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

### Các kiểu nguồn của watch {#watch-source-types}

Tham số đầu tiên của `watch` có thể là nhiều loại "nguồn" reactive khác nhau: có thể là một ref (bao gồm cả computed ref), một object reactive, một getter function, hoặc một array chứa nhiều nguồn:

```js
const x = ref(0)
const y = ref(0)

// ref đơn
watch(x, (newX) => {
  console.log(`x là ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`tổng x + y là: ${sum}`)
  }
)

// nhiều nguồn
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x là ${newX} và y là ${newY}`)
})
```

Lưu ý rằng bạn không thể watch trực tiếp một property của object reactive như sau:

```js
const obj = reactive({ count: 0 })

// không hoạt động vì truyền vào một số
watch(obj.count, (count) => {
  console.log(`Count là: ${count}`)
})
```

Thay vào đó, hãy dùng getter:

```js
watch(
  () => obj.count,
  (count) => {
    console.log(`Count là: ${count}`)
  }
)
```

</div>

## Deep Watchers {#deep-watchers}

<div class="options-api">

`watch` mặc định là shallow: callback chỉ chạy khi property được gán giá trị mới, không chạy khi property lồng nhau thay đổi. Nếu muốn theo dõi mọi thay đổi lồng nhau, cần dùng deep watcher:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Lưu ý: newValue sẽ bằng oldValue nếu object không bị thay thế
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Khi gọi `watch()` trực tiếp trên object reactive, nó sẽ tự động là deep watcher:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // chạy khi property lồng nhau thay đổi
})

obj.count++
```

Phân biệt với getter trả về object reactive: callback chỉ chạy khi object bị thay thế.

```js
watch(
  () => state.someObject,
  () => {
    // chỉ chạy khi object bị replace
  }
)
```

Có thể ép thành deep watcher:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // newValue === oldValue nếu không replace
  },
  { deep: true }
)
```

</div>

Trong Vue 3.5+, option `deep` có thể là số, chỉ mức độ traverse.

:::warning Lưu ý
Deep watch có thể tốn tài nguyên với dữ liệu lớn.
:::

## Eager Watchers {#eager-watchers}

`watch` mặc định lazy: chỉ chạy khi source thay đổi. Tuy nhiên, trong một số trường hợp chúng ta muốn chạy cùng logic callback ngay lập tức — ví dụ, muốn fetch dữ liệu ban đầu, rồi fetch lại khi state liên quan thay đổi.

<div class="options-api">

Có thể ép callback chạy ngay bằng cách khai báo watcher dưới dạng object với `handler` function và option `immediate: true`:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // chạy ngay khi tạo component
      },
      // ép chạy callback ngay lập tức
      immediate: true
    }
  }
  // ...
}
```

Lần chạy đầu tiên của handler function sẽ diễn ra ngay trước hook `created`. Vue đã xử lý xong các option `data`, `computed` và `methods`, nên những property đó sẽ có sẵn ở lần gọi đầu tiên.

</div>

<div class="composition-api">

Có thể ép callback chạy ngay bằng option `immediate: true`:

```js
watch(source, callback, { immediate: true })
```

</div>

## Once Watchers {#once-watchers}

- Chỉ hỗ trợ từ 3.4+

Callback của watcher sẽ chạy mỗi khi source thay đổi. Nếu muốn callback chỉ chạy một lần duy nhất khi source thay đổi, hãy dùng option `once: true`.

<div class="options-api">

```js
export default {
  watch: {
    source: {
      handler(newValue, oldValue) {
        // khi `source` thay đổi, chỉ chạy một lần
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
    // khi `source` thay đổi, chỉ chạy một lần
  },
  { once: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Thường gặp trường hợp callback của watcher dùng đúng cùng reactive state như source. Ví dụ, xem đoạn code sau dùng watcher để load remote resource mỗi khi ref `todoId` thay đổi:

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

Đáng chú ý là watcher dùng `todoId` hai lần: một lần làm source, rồi lại dùng trong callback.

Điều này có thể đơn giản hóa với [`watchEffect()`](/api/reactivity-core#watcheffect). `watchEffect()` cho phép chúng ta tự động track các reactive dependency của callback. Watcher trên có thể viết lại như sau:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Ở đây, callback sẽ chạy ngay lập tức, không cần chỉ định `immediate: true`. Trong quá trình chạy, nó sẽ tự động track `todoId.value` như một dependency (tương tự computed property). Mỗi khi `todoId.value` thay đổi, callback sẽ chạy lại. Với `watchEffect()`, chúng ta không cần truyền `todoId` tường minh làm source value nữa.

Bạn có thể xem [ví dụ này](/examples/#fetching-data) về `watchEffect()` và reactive data-fetching trong thực tế.

Với những ví dụ như trên, chỉ có một dependency, lợi ích của `watchEffect()` còn tương đối nhỏ. Nhưng với các watcher có nhiều dependency, dùng `watchEffect()` giúp loại bỏ gánh nặng phải duy trì danh sách dependency thủ công. Ngoài ra, nếu cần watch nhiều property trong một cấu trúc dữ liệu lồng nhau, `watchEffect()` có thể hiệu quả hơn deep watcher vì nó chỉ track các property thực sự được dùng trong callback, thay vì theo dõi đệ quy tất cả.

:::tip
`watchEffect` chỉ track dependency trong quá trình thực thi **đồng bộ**. Khi dùng với async callback, chỉ những property được truy cập trước `await` đầu tiên mới được track.
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

`watch` và `watchEffect` đều cho phép thực hiện side effects (tác dụng phụ) một cách reactive. Sự khác biệt chính là cách chúng track reactive dependency:

- `watch` chỉ track source được watch tường minh. Nó không track bất kỳ thứ gì được truy cập bên trong callback. Ngoài ra, callback chỉ chạy khi source thực sự thay đổi. `watch` tách biệt dependency tracking khỏi side effect, giúp kiểm soát chính xác hơn khi nào callback nên chạy.

- `watchEffect`, ngược lại, kết hợp dependency tracking và side effect thành một giai đoạn. Nó tự động track mọi reactive property được truy cập trong quá trình thực thi đồng bộ. Code ngắn gọn hơn và thường tiện lợi hơn, nhưng reactive dependency của nó ít tường minh hơn.

</div>

## Cleanup side effect {#side-effect-cleanup}

Đôi khi chúng ta thực hiện side effects, ví dụ như gửi request bất đồng bộ, trong một watcher:

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

Nhưng nếu `id` thay đổi trước khi request hoàn thành thì sao? Khi request cũ hoàn thành, nó vẫn sẽ gọi callback với giá trị ID đã lỗi thời. Lý tưởng là chúng ta muốn hủy request cũ khi `id` thay đổi sang giá trị mới.

Chúng ta có thể dùng API [`onWatcherCleanup()`](/api/reactivity-core#onwatchercleanup) <sup class="vt-badge" data-text="3.5+" /> để đăng ký một cleanup function sẽ được gọi khi watcher bị invalidate và sắp chạy lại:

<div class="composition-api">

```js {10-13}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()

  fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
    // callback logic
  })

  onWatcherCleanup(() => {
    // hủy request cũ
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
        // hủy request cũ
        controller.abort()
      })
    }
  }
}
```

</div>

Lưu ý rằng `onWatcherCleanup` chỉ được hỗ trợ từ Vue 3.5+ và phải được gọi trong quá trình thực thi đồng bộ của effect function của `watchEffect` hoặc callback function của `watch`: bạn không thể gọi nó sau một câu lệnh `await` trong async function.

Ngoài ra, một function `onCleanup` cũng được truyền vào callback của watcher dưới dạng tham số thứ 3<span class="composition-api">, và vào effect function của `watchEffect` dưới dạng tham số đầu tiên</span>:

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

`onCleanup` được truyền qua tham số function bị ràng buộc với watcher instance, nên nó không bị ràng buộc bởi yêu cầu đồng bộ của `onWatcherCleanup`.

## Thời điểm chạy callback {#callback-flush-timing}

Khi bạn thay đổi reactive state, nó có thể kích hoạt cả Vue component update lẫn watcher callback do bạn tạo ra.

Tương tự component update, các watcher callback do người dùng tạo được gộp batch lại để tránh gọi trùng lặp. Ví dụ, chúng ta không muốn watcher chạy một nghìn lần nếu chúng ta đẩy đồng bộ một nghìn phần tử vào một array đang được watch.

Theo mặc định, callback của watcher được gọi **sau** khi component cha update (nếu có), và **trước** khi DOM của component owner update. Điều này có nghĩa là nếu bạn cố truy cập DOM của component owner bên trong callback của watcher, DOM sẽ ở trạng thái chưa được cập nhật.

### Post Watchers {#post-watchers}

Nếu muốn truy cập DOM của component owner trong callback của watcher **sau khi** Vue đã cập nhật nó, bạn cần chỉ định option `flush: 'post'`:

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

Post-flush `watchEffect()` cũng có alias tiện lợi là `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* chạy sau khi Vue cập nhật */
})
```

</div>

### Sync Watchers {#sync-watchers}

Cũng có thể tạo watcher chạy đồng bộ, trước bất kỳ update nào do Vue quản lý:

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
  /* chạy đồng bộ khi reactive data thay đổi */
})
```

</div>

:::warning Dùng với thận trọng
Sync watcher không batch và chạy mỗi khi phát hiện reactive mutation. Dùng chúng để watch các giá trị boolean đơn giản là ổn, nhưng tránh dùng với các nguồn dữ liệu có thể bị thay đổi đồng bộ nhiều lần, ví dụ như array.
:::

<div class="options-api">

## `this.$watch()` \* {#this-watch}

Cũng có thể tạo watcher theo lập trình bằng [instance method `$watch()`](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Cách này hữu ích khi cần thiết lập watcher có điều kiện, hoặc chỉ watch khi có tương tác từ người dùng. Nó cũng cho phép dừng watcher sớm hơn.

</div>

## Dừng watcher {#stopping-a-watcher}

<div class="options-api">

Các watcher khai báo với option `watch` hoặc instance method `$watch()` sẽ tự động dừng khi component owner unmount, nên trong hầu hết trường hợp bạn không cần lo việc dừng watcher thủ công.

Trong trường hợp hiếm gặp cần dừng watcher trước khi component unmount, API `$watch()` trả về một function để làm điều đó:

```js
const unwatch = this.$watch('foo', callback)

// ...khi watcher không còn cần thiết:
unwatch()
```

</div>

<div class="composition-api">

Các watcher khai báo đồng bộ bên trong `setup()` hoặc `<script setup>` được ràng buộc với component instance owner, và sẽ tự động dừng khi component owner unmount. Trong hầu hết trường hợp, bạn không cần lo việc dừng watcher thủ công.

Điều quan trọng ở đây là watcher phải được tạo **đồng bộ**: nếu watcher được tạo trong một async callback, nó sẽ không ràng buộc với component owner và phải được dừng thủ công để tránh memory leak. Đây là ví dụ:

```vue
<script setup>
import { watchEffect } from 'vue'

// watcher này sẽ tự động dừng
watchEffect(() => {})

// ...watcher này thì không!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Để dừng watcher thủ công, hãy dùng handle function được trả về. Cách này hoạt động với cả `watch` lẫn `watchEffect`:

```js
const unwatch = watchEffect(() => {})

// ...sau này, khi không còn cần thiết
unwatch()
```

[Thử trong Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

Lưu ý rằng sẽ rất hiếm khi bạn cần tạo watcher bất đồng bộ, và nên ưu tiên tạo đồng bộ bất cứ khi nào có thể. Nếu cần chờ dữ liệu bất đồng bộ, bạn có thể làm logic watch có điều kiện thay thế:

```js
// dữ liệu được load bất đồng bộ
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // thực hiện gì đó khi dữ liệu đã load xong
  }
})
```

</div>
