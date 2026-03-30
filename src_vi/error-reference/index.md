<script setup>
import { ref, onMounted } from 'vue'
import { data } from './errors.data.ts'
import ErrorsTable from './ErrorsTable.vue'

const highlight = ref()
onMounted(() => {
  highlight.value = location.hash.slice(1)
})
</script>

# Tra cứu mã lỗi production {#error-reference}

## Lỗi runtime {#runtime-errors}

Trong bản build production, đối số thứ 3 truyền vào các API xử lý lỗi dưới đây sẽ là một mã ngắn thay vì chuỗi thông tin đầy đủ:

- [`app.config.errorHandler`](/api/application#app-config-errorhandler)
- [`onErrorCaptured`](/api/composition-api-lifecycle#onerrorcaptured) (Composition API)
- [`errorCaptured`](/api/options-lifecycle#errorcaptured) (Options API)

Bảng dưới đây ánh xạ các mã đó về chuỗi thông tin đầy đủ ban đầu của chúng.

<ErrorsTable kind="runtime" :errors="data.runtime" :highlight="highlight" />

## Lỗi compiler {#compiler-errors}

Bảng dưới đây cung cấp ánh xạ từ các mã lỗi compiler trong production sang thông báo gốc của chúng.

<ErrorsTable kind="compiler" :errors="data.compiler" :highlight="highlight" />
