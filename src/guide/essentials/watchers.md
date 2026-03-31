Watchers {#watchers}

Ví dụ cơ bản {#basic-example}

Computed properties cho phép chúng ta tính toán các giá trị dẫn xuất một cách khai báo. Tuy nhiên, có những trường hợp chúng ta cần thực hiện các “side effects” (tác dụng phụ) khi trạng thái thay đổi, ví dụ như thay đổi DOM, hoặc cập nhật một phần state khác dựa trên kết quả của một thao tác bất đồng bộ.

<div class="options-api">


Với Options API, chúng ta có thể dùng option watch￼ để kích hoạt một function mỗi khi một property reactive thay đổi:

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

<p>
  Hỏi một câu hỏi yes/no:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>

Thử trong Playground￼

Option watch cũng hỗ trợ đường dẫn dạng dot làm key:

export default {
  watch: {
    // Lưu ý: chỉ hỗ trợ đường dẫn đơn giản. Không hỗ trợ biểu thức.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}

</div>


<div class="composition-api">


Với Composition API, chúng ta có thể dùng function watch￼ để kích hoạt callback mỗi khi một phần state reactive thay đổi:

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

Thử trong Playground￼

Các kiểu nguồn của watch {#watch-source-types}

Tham số đầu tiên của watch có thể là nhiều loại “nguồn” reactive khác nhau: có thể là một ref (bao gồm cả computed ref), một object reactive, một getter function, hoặc một array chứa nhiều nguồn:

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

Lưu ý rằng bạn không thể watch trực tiếp một property của object reactive như sau:

const obj = reactive({ count: 0 })

// không hoạt động vì truyền vào một số
watch(obj.count, (count) => {
  console.log(`Count là: ${count}`)
})

Thay vào đó, hãy dùng getter:

watch(
  () => obj.count,
  (count) => {
    console.log(`Count là: ${count}`)
  }
)

</div>


Deep Watchers {#deep-watchers}

<div class="options-api">


watch mặc định là shallow: callback chỉ chạy khi property được gán giá trị mới, không chạy khi property lồng nhau thay đổi. Nếu muốn theo dõi mọi thay đổi lồng nhau, cần dùng deep watcher:

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

</div>


<div class="composition-api">


Khi gọi watch() trực tiếp trên object reactive, nó sẽ tự động là deep watcher:

const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // chạy khi property lồng nhau thay đổi
})

obj.count++

Phân biệt với getter trả về object reactive: callback chỉ chạy khi object bị thay thế.

watch(
  () => state.someObject,
  () => {
    // chỉ chạy khi object bị replace
  }
)

Có thể ép thành deep watcher:

watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // newValue === oldValue nếu không replace
  },
  { deep: true }
)

</div>


Trong Vue 3.5+, option deep có thể là số, chỉ mức độ traverse.

:::warning Lưu ý
Deep watch có thể tốn tài nguyên với dữ liệu lớn.
:::

Eager Watchers {#eager-watchers}

watch mặc định lazy: chỉ chạy khi source thay đổi.

Có thể chạy ngay bằng immediate: true.

<div class="options-api">


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

</div>


<div class="composition-api">


watch(source, callback, { immediate: true })

</div>


Once Watchers {#once-watchers}
	•	Chỉ hỗ trợ từ 3.4+

Chạy callback chỉ một lần:

watch(source, callback, { once: true })

<div class="composition-api">


watchEffect() ** {#watcheffect}

watchEffect() tự động track dependency:

watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})

Callback chạy ngay và track dependency.

:::tip
Chỉ track trước await
:::

watch vs watchEffect {#watch-vs-watcheffect}
	•	watch: explicit dependency, kiểm soát tốt
	•	watchEffect: tự động, code ngắn hơn

</div>


Cleanup side effect {#side-effect-cleanup}

Dùng onWatcherCleanup:

onWatcherCleanup(() => {
  controller.abort()
})

Hoặc onCleanup trong callback.

Thời điểm chạy callback {#callback-flush-timing}

Mặc định chạy trước khi DOM update của component.

Post watcher

watch(source, callback, { flush: 'post' })

Sync watcher

watch(source, callback, { flush: 'sync' })

:::warning
Sync watcher không batch, cần cẩn thận
:::

Dừng watcher {#stopping-a-watcher}

Watcher tự dừng khi component unmount.

Có thể dừng thủ công:

const unwatch = watchEffect(() => {})
unwatch()

￼