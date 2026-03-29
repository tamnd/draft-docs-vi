# Watchers {#watchers}

## Ví dụ cơ bản {#basic-example}

Thuộc tính computed cho phép ta khai báo giá trị dẫn xuất một cách rõ ràng. Tuy vậy, có những trường hợp ta cần thực hiện "side effect" khi state thay đổi, ví dụ sửa DOM, hoặc cập nhật một phần state khác dựa trên kết quả của một thao tác async.

<div class="options-api">

Với Options API, ta có thể dùng [option `watch`](/api/options-state#watch) để kích hoạt một hàm mỗi khi một property phản ứng thay đổi:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Câu hỏi thường sẽ có dấu chấm hỏi. ;-)',
      loading: false
    }
  },
  watch: {
    // mỗi khi question thay đổi, hàm này sẽ chạy
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
        this.answer = 'Lỗi! Không thể gọi tới API. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  Hãy hỏi một câu có / không:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9VE1v2zAM/SucLnaw1D70lqUbsiKH7rB1W4++aDYdq5ElTx9xgiD/fbT8lXZFAQO2+Mgn8pH0mW2aJjl4ZCu2trkRjfucKTw22jgosOReOjhnCqDgjseL/hvAoPNGjSeAvx6tE1qtIIqWo5Er26Ih088BteCt51KeINfKcaGAT5FQc7NP4NPNYiaQmhdC7VZQcmlxMF+61yUcWu7yajVmkabQVqjwgGZmzSuudmiX4CphofQqD+ZWSAnGqz5y9I4VtmOuS9CyGA9T3QCihGu3RKhc+gJtHH2JFld+EG5Mdug2QYZ4MSKhgBd11OgqXdipEm5PKoer0Jk2kA66wB044/EF1GtOSPRUCbUnryRJosnFnK4zpC5YR7205M9bLhyUSIrGUeVcY1dpekKrdNK6MuWNiKYKXt8V98FElDxbknGxGLCpZMi7VkGMxmjzv0pz1tvO4QPcay8LULoj5RToKoTN40MCEXyEQDJTl0KFmXpNOqsUxudN+TNFzzqdJp8ODutGcod0Alg34QWwsXsaVtIjVXqe9h5bC9V4B4ebWhco7zI24hmDVSEs/yOxIPOQEFnTnjzt2emS83nYFrhcevM6nRJhS+Ys9aoUu6Av7WqoNWO5rhsh0fxownplbBqhjJEmuv0WbN2UDNtDMRXm+zfsz/bY2TL2SH1Ec8CMTZjjhqaxh7e/v+ORvieQqvaSvN8Bf6HV0veSdG5fvSoo7Su/kO1D3f13SKInuz06VHYsahzzfl0yRj+s+3dKn9O9TW7HPrPLP624lFU=)

Option `watch` cũng hỗ trợ một đường dẫn phân tách bằng dấu chấm làm key:

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

Với Composition API, ta có thể dùng [hàm `watch`](/api/reactivity-core#watch) để kích hoạt một callback mỗi khi một phần state phản ứng thay đổi:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Câu hỏi thường sẽ có dấu chấm hỏi. ;-)')
const loading = ref(false)

// watch hoạt động trực tiếp với ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Đang suy nghĩ...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Lỗi! Không thể gọi tới API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Hãy hỏi một câu có / không:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

### Kiểu nguồn theo dõi {#watch-source-types}

Đối số đầu tiên của `watch` có thể là nhiều kiểu "nguồn" phản ứng khác nhau: một ref (bao gồm cả computed ref), một object phản ứng, một [hàm getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), hoặc một mảng gồm nhiều nguồn:

```js
const x = ref(0)
const y = ref(0)

// một ref đơn lẻ
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

// mảng gồm nhiều nguồn
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x là ${newX} và y là ${newY}`)
})
```

Lưu ý là bạn không thể theo dõi một property của object phản ứng theo cách này:

```js
const obj = reactive({ count: 0 })

// cách này không chạy vì ta đang truyền một con số vào watch()
watch(obj.count, (count) => {
  console.log(`Count là: ${count}`)
})
```

Thay vào đó, hãy dùng một getter:

```js
// thay vào đó, dùng getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`Count là: ${count}`)
  }
)
```

</div>

## Deep Watcher {#deep-watchers}

<div class="options-api">

`watch` mặc định là nông: callback chỉ chạy khi property đang được theo dõi được gán một giá trị mới, chứ không chạy khi property lồng bên trong thay đổi. Nếu muốn callback phản ứng với mọi thay đổi lồng nhau, bạn cần dùng deep watcher:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Lưu ý: ở đây `newValue` sẽ bằng `oldValue`
        // khi thay đổi xảy ra ở property lồng bên trong,
        // miễn là bản thân object chưa bị thay thế.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Khi bạn gọi `watch()` trực tiếp trên một object phản ứng, nó sẽ ngầm tạo ra một deep watcher, nghĩa là callback sẽ chạy với mọi thay đổi lồng bên trong:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // chạy khi property lồng bên trong bị thay đổi
  // Lưu ý: `newValue` sẽ bằng `oldValue`
  // vì cả hai cùng trỏ tới một object!
})

obj.count++
```

Điều này khác với trường hợp dùng một getter trả về object phản ứng. Trong trường hợp đó, callback chỉ chạy nếu getter trả về một object khác:

```js
watch(
  () => state.someObject,
  () => {
    // chỉ chạy khi state.someObject bị thay thế
  }
)
```

Tuy vậy, bạn vẫn có thể ép trường hợp thứ hai thành deep watcher bằng cách dùng rõ ràng option `deep`:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Lưu ý: ở đây `newValue` sẽ bằng `oldValue`
    // *trừ khi* state.someObject đã bị thay thế
  },
  { deep: true }
)
```

</div>

Trong Vue 3.5+, option `deep` cũng có thể là một con số để chỉ độ sâu duyệt tối đa, tức là Vue sẽ đi sâu bao nhiêu tầng vào các property lồng bên trong của object.

:::warning Dùng cẩn thận
Deep watch buộc Vue phải duyệt qua mọi property lồng bên trong của object được theo dõi, và có thể khá tốn kém nếu dùng với cấu trúc dữ liệu lớn. Chỉ dùng khi thật sự cần, và lưu ý tới ảnh hưởng về hiệu năng.
:::

## Watcher chạy ngay {#eager-watchers}

`watch` mặc định là lazy: callback sẽ không được gọi cho tới khi nguồn đang theo dõi thay đổi. Nhưng có những lúc ta muốn cùng một callback được chạy ngay từ đầu, ví dụ tải dữ liệu ban đầu, rồi tải lại mỗi khi state liên quan thay đổi.

<div class="options-api">

Ta có thể buộc callback của watcher chạy ngay lập tức bằng cách khai báo nó dưới dạng object với hàm `handler` và option `immediate: true`:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // đoạn này sẽ chạy ngay khi component được tạo.
      },
      // buộc callback chạy ngay
      immediate: true
    }
  }
  // ...
}
```

Lần chạy đầu tiên của hàm handler sẽ diễn ra ngay trước hook `created`. Khi đó Vue đã xử lý xong các option `data`, `computed` và `methods`, nên những property đó đã sẵn sàng ở lần gọi đầu tiên.

</div>

<div class="composition-api">

Ta có thể buộc callback của watcher chạy ngay bằng cách truyền option `immediate: true`:

```js
watch(
  source,
  (newValue, oldValue) => {
    // chạy ngay lập tức, sau đó chạy lại khi `source` thay đổi
  },
  { immediate: true }
)
```

</div>

## Watcher chỉ chạy một lần {#once-watchers}

- Chỉ được hỗ trợ từ 3.4+

Callback của watcher sẽ chạy mỗi khi nguồn theo dõi thay đổi. Nếu bạn muốn callback chỉ chạy đúng một lần khi nguồn thay đổi, hãy dùng option `once: true`.

<div class="options-api">

```js
export default {
  watch: {
    source: {
      handler(newValue, oldValue) {
        // khi `source` thay đổi, chỉ chạy đúng một lần
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
    // khi `source` thay đổi, chỉ chạy đúng một lần
  },
  { once: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Trong thực tế, callback của watcher thường dùng đúng phần state phản ứng đang được lấy làm nguồn. Ví dụ, hãy xem đoạn code sau. Nó dùng watcher để tải một tài nguyên từ xa mỗi khi ref `todoId` thay đổi:

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

Hãy để ý là watcher này dùng `todoId` hai lần: một lần làm nguồn theo dõi, và một lần nữa trong callback.

Ta có thể viết gọn hơn với [`watchEffect()`](/api/reactivity-core#watcheffect). `watchEffect()` cho phép tự động theo dõi dependency phản ứng mà callback dùng tới. Watcher ở trên có thể viết lại như sau:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Ở đây, callback sẽ chạy ngay, nên không cần chỉ định `immediate: true`. Trong lúc chạy, nó sẽ tự động theo dõi `todoId.value` như một dependency, khá giống computed. Mỗi khi `todoId.value` thay đổi, callback sẽ chạy lại. Với `watchEffect()`, ta không còn phải truyền `todoId` một cách tường minh làm giá trị nguồn nữa.

Bạn có thể xem [ví dụ này](/examples/#fetching-data) để thấy `watchEffect()` và việc tải dữ liệu phản ứng hoạt động như thế nào.

Với ví dụ chỉ có một dependency như trên, lợi ích của `watchEffect()` chưa quá rõ. Nhưng với watcher phụ thuộc vào nhiều dependency, `watchEffect()` sẽ giúp bạn đỡ phải tự duy trì danh sách dependency bằng tay. Ngoài ra, nếu bạn cần theo dõi nhiều property trong một cấu trúc dữ liệu lồng nhau, `watchEffect()` có thể còn hiệu quả hơn deep watcher, vì nó chỉ theo dõi những property thực sự được dùng trong callback, thay vì đệ quy qua tất cả.

:::tip
`watchEffect` chỉ theo dõi dependency trong quá trình chạy **đồng bộ** của nó. Khi dùng cùng callback async, chỉ những property được truy cập trước `await` đầu tiên mới được theo dõi.
:::

### `watch` và `watchEffect` {#watch-vs-watcheffect}

`watch` và `watchEffect` đều cho phép ta thực hiện side effect theo kiểu phản ứng. Khác biệt chính nằm ở cách chúng theo dõi dependency phản ứng:

- `watch` chỉ theo dõi nguồn được khai báo rõ ràng. Nó không theo dõi những gì được truy cập bên trong callback. Ngoài ra, callback chỉ chạy khi nguồn thực sự thay đổi. `watch` tách riêng việc theo dõi dependency khỏi side effect, nên cho bạn quyền kiểm soát chính xác hơn về thời điểm callback nên chạy.
- `watchEffect` thì ngược lại, gộp việc theo dõi dependency và side effect vào cùng một bước. Nó tự động theo dõi mọi property phản ứng được truy cập trong lúc chạy đồng bộ. Cách này tiện hơn và thường cho code ngắn hơn, nhưng cũng làm dependency phản ứng bớt tường minh hơn.

</div>

## Dọn dẹp side effect {#side-effect-cleanup}

Đôi khi ta thực hiện side effect, ví dụ các request async, bên trong watcher:

<div class="composition-api">

```js
watch(id, (newId) => {
  fetch(`/api/${newId}`).then(() => {
    // logic callback
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
        // logic callback
      })
    }
  }
}
```

</div>

Nhưng nếu `id` đổi trước khi request hoàn tất thì sao? Khi request cũ xong, nó vẫn sẽ chạy callback với một giá trị ID đã lỗi thời. Lý tưởng nhất là ta có thể hủy request cũ khi `id` đổi sang giá trị mới.

Ta có thể dùng API [`onWatcherCleanup()`](/api/reactivity-core#onwatchercleanup) <sup class="vt-badge" data-text="3.5+" /> để đăng ký một hàm dọn dẹp. Hàm này sẽ được gọi khi watcher bị vô hiệu hóa và sắp chạy lại:

<div class="composition-api">

```js {10-13}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()

  fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
    // logic callback
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
        // logic callback
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

Lưu ý `onWatcherCleanup` chỉ được hỗ trợ từ Vue 3.5+ và phải được gọi trong quá trình chạy đồng bộ của hàm effect `watchEffect` hoặc callback `watch`: bạn không thể gọi nó sau một câu lệnh `await` trong hàm async.

Ngoài ra, một hàm `onCleanup` cũng được truyền vào callback của watcher dưới dạng đối số thứ ba<span class="composition-api">, và được truyền vào effect function của `watchEffect` dưới dạng đối số đầu tiên</span>:

<div class="composition-api">

```js
watch(id, (newId, oldId, onCleanup) => {
  // ...
  onCleanup(() => {
    // logic dọn dẹp
  })
})

watchEffect((onCleanup) => {
  // ...
  onCleanup(() => {
    // logic dọn dẹp
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
        // logic dọn dẹp
      })
    }
  }
}
```

</div>

`onCleanup` được truyền qua đối số hàm sẽ gắn với instance watcher, nên không bị ràng buộc bởi yêu cầu phải gọi đồng bộ như `onWatcherCleanup`.

## Thời điểm flush của callback {#callback-flush-timing}

Khi bạn thay đổi state phản ứng, điều đó có thể kích hoạt cả việc cập nhật component Vue lẫn callback watcher do bạn tự tạo.

Tương tự cập nhật component, callback watcher do người dùng tạo ra cũng được gom lô để tránh bị gọi trùng. Ví dụ, ta hẳn không muốn một watcher chạy cả nghìn lần nếu đồng bộ thêm một nghìn phần tử vào một mảng đang được theo dõi.

Mặc định, callback của watcher được gọi **sau** khi component cha cập nhật xong (nếu có), và **trước** khi DOM của component sở hữu nó được cập nhật. Điều đó có nghĩa là nếu bạn cố truy cập DOM của chính component đó bên trong callback watcher, bạn sẽ thấy DOM ở trạng thái trước cập nhật.

### Post Watcher {#post-watchers}

Nếu bạn muốn truy cập DOM của component sở hữu watcher trong callback **sau khi** Vue đã cập nhật xong, bạn cần chỉ định option `flush: 'post'`:

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

`watchEffect()` với chế độ post-flush cũng có một tên gọi tắt tiện hơn là `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* chạy sau khi Vue cập nhật */
})
```

</div>

### Sync Watcher {#sync-watchers}

Ta cũng có thể tạo một watcher chạy đồng bộ, trước mọi cập nhật do Vue quản lý:

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

`watchEffect()` kiểu sync cũng có một tên gọi tắt là `watchSyncEffect()`:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* chạy đồng bộ khi dữ liệu phản ứng thay đổi */
})
```

</div>

:::warning Dùng cẩn thận
Sync watcher không có cơ chế gom lô và sẽ kích hoạt mỗi khi phát hiện một thay đổi phản ứng. Bạn có thể dùng chúng để theo dõi những giá trị boolean đơn giản, nhưng nên tránh dùng với nguồn dữ liệu có thể bị thay đổi đồng bộ nhiều lần, ví dụ như mảng.
:::

<div class="options-api">

## `this.$watch()` \* {#this-watch}

Ta cũng có thể tạo watcher theo kiểu mệnh lệnh bằng [instance method `$watch()`](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Cách này hữu ích khi bạn cần chỉ thiết lập watcher trong một điều kiện nào đó, hoặc chỉ theo dõi thứ gì đó để phản hồi tương tác của người dùng. Nó cũng cho phép bạn dừng watcher sớm.

</div>

## Dừng một watcher {#stopping-a-watcher}

<div class="options-api">

Watcher được khai báo bằng option `watch` hoặc instance method `$watch()` sẽ tự động dừng khi component sở hữu nó bị unmount, nên trong đa số trường hợp bạn không cần tự lo việc dừng watcher.

Trong một vài trường hợp hiếm hoi khi bạn cần dừng watcher trước lúc component bị unmount, API `$watch()` sẽ trả về một hàm để làm điều đó:

```js
const unwatch = this.$watch('foo', callback)

// ...khi không còn cần watcher nữa:
unwatch()
```

</div>

<div class="composition-api">

Watcher được khai báo đồng bộ bên trong `setup()` hoặc `<script setup>` sẽ được gắn với instance của component sở hữu nó, và sẽ tự động dừng khi component đó bị unmount. Trong đa số trường hợp, bạn không cần tự lo chuyện dừng watcher.

Điểm quan trọng ở đây là watcher phải được tạo **đồng bộ**: nếu watcher được tạo trong một callback async, nó sẽ không gắn với component sở hữu và phải được dừng thủ công để tránh rò rỉ bộ nhớ. Ví dụ:

```vue
<script setup>
import { watchEffect } from 'vue'

// watcher này sẽ được tự động dừng
watchEffect(() => {})

// ...còn watcher này thì không!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Để dừng watcher thủ công, hãy dùng hàm handle được trả về. Cách này dùng được cho cả `watch` và `watchEffect`:

```js
const unwatch = watchEffect(() => {})

// ...về sau, khi không còn cần nữa
unwatch()
```

Lưu ý là số trường hợp cần tạo watcher theo kiểu async nên rất ít, và bạn nên ưu tiên tạo đồng bộ bất cứ khi nào có thể. Nếu bạn cần chờ một dữ liệu async nào đó, hãy cân nhắc viết logic theo dõi theo kiểu có điều kiện:

```js
// dữ liệu sẽ được tải bất đồng bộ
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // làm gì đó khi dữ liệu đã được tải xong
  }
})
```

</div>
