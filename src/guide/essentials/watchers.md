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

`watch` mặc định lazy: chỉ chạy khi source thay đổi.

Có thể chạy ngay bằng `immediate: true`.

<div class="options-api">

```js
export default {
  watch: {
    question: {
      handler(newQuestion) {
        // chạy ngay khi tạo component
      },
      immediate: true
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(source, callback, { immediate: true })
```

</div>

## Once Watchers {#once-watchers}

- Chỉ hỗ trợ từ 3.4+

Chạy callback chỉ một lần:

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
watch(source, callback, { once: true })
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

`watchEffect()` tự động track dependency:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Callback chạy ngay và track dependency.

:::tip
Chỉ track trước `await`
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

- `watch`: explicit dependency, kiểm soát tốt
- `watchEffect`: tự động, code ngắn hơn

</div>

## Cleanup side effect {#side-effect-cleanup}

Dùng `onWatcherCleanup`:

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

Hoặc `onCleanup` trong callback.

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

## Thời điểm chạy callback {#callback-flush-timing}

Mặc định chạy trước khi DOM update của component.

### Post Watchers {#post-watchers}

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

Post-flush `watchEffect()` also has a convenience alias, `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* executed after Vue updates */
})
```

</div>

### Sync Watchers {#sync-watchers}

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

Sync `watchEffect()` also has a convenience alias, `watchSyncEffect()`:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* executed synchronously upon reactive data change */
})
```

</div>

:::warning
Sync watcher không batch, cần cẩn thận
:::

## Dừng watcher {#stopping-a-watcher}

Watcher tự dừng khi component unmount.

Có thể dừng thủ công:

<div class="composition-api">

```js
const unwatch = watchEffect(() => {})

// ...later, when no longer needed
unwatch()
```

</div>

<div class="options-api">

```js
const unwatch = this.$watch('foo', callback)

// ...when the watcher is no longer needed:
unwatch()
```

</div>

<div class="composition-api">

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

[Thử trong Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

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
