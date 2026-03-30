# Composable {#composables}

<script setup>
import { useMouse } from './mouse'
const { x, y } = useMouse()
</script>

:::tip
Phần này giả định rằng bạn đã có kiến thức cơ bản về Composition API. Nếu bạn chỉ mới học Vue với Options API, bạn có thể chuyển API Preference sang Composition API (bằng nút gạt ở đầu thanh bên trái), rồi đọc lại các chương [Nền tảng về tính phản ứng](/guide/essentials/reactivity-fundamentals) và [Hook vòng đời](/guide/essentials/lifecycle).
:::

## "Composable" Là Gì? {#what-is-a-composable}

Trong ngữ cảnh ứng dụng Vue, "composable" là một hàm tận dụng Composition API của Vue để bao bọc và tái sử dụng **logic có state**.

Khi xây dựng ứng dụng frontend, ta thường cần tái sử dụng logic cho những tác vụ phổ biến. Ví dụ, ta có thể cần định dạng ngày tháng ở nhiều nơi, vì vậy ta sẽ tách ra một hàm dùng lại được cho mục đích đó. Hàm formatter này bao bọc **logic không có state**: nó nhận một đầu vào và ngay lập tức trả về đầu ra mong muốn. Có rất nhiều thư viện ngoài kia dùng để tái sử dụng logic không có state, chẳng hạn [lodash](https://lodash.com/) và [date-fns](https://date-fns.org/).

Ngược lại, logic có state liên quan đến việc quản lý trạng thái thay đổi theo thời gian. Một ví dụ đơn giản là theo dõi vị trí hiện tại của chuột trên trang. Trong tình huống thực tế, nó cũng có thể là logic phức tạp hơn, như thao tác cảm ứng hoặc trạng thái kết nối tới cơ sở dữ liệu.

## Ví Dụ Theo Dõi Chuột {#mouse-tracker-example}

Nếu triển khai chức năng theo dõi chuột trực tiếp trong component bằng Composition API, nó sẽ trông như sau:

```vue [MouseComponent.vue]
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

Nhưng nếu ta muốn tái sử dụng cùng logic đó ở nhiều component thì sao? Ta có thể tách logic ra một file bên ngoài dưới dạng hàm composable:

```js [mouse.js]
import { ref, onMounted, onUnmounted } from 'vue'

// theo quy ước, tên composable bắt đầu bằng "use"
export function useMouse() {
  // state được composable bao bọc và quản lý
  const x = ref(0)
  const y = ref(0)

  // composable có thể cập nhật state mà nó quản lý theo thời gian.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // composable cũng có thể móc vào vòng đời của component sở hữu nó
  // để thiết lập và dọn dẹp side effect.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // lộ state được quản lý ra ngoài bằng giá trị trả về
  return { x, y }
}
```

Và đây là cách nó được dùng trong component:

```vue [MouseComponent.vue]
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

<div class="demo">
  Mouse position is at: {{ x }}, {{ y }}
</div>

[Thử trên Playground](https://play.vuejs.org/#eNqNkj1rwzAQhv/KocUOGKVzSAIdurVjoQUvJj4XlfgkJNmxMfrvPcmJkkKHLrbu69H7SlrEszFyHFDsxN6drDIeHPrBHGtSvdHWwwKDwzfNHwjQWd1DIbd9jOW3K2qq6aTJxb6pgpl7Dnmg3NS0365YBnLgsTfnxiNHACvUaKe80gTKQeN3sDAIQqjignEhIvKYqMRta1acFVrsKtDEQPLYxuU7cV8Msmg2mdTilIa6gU5p27tYWKKq1c3ENphaPrGFW25+yMXsHWFaFlfiiOSvFIBJjs15QJ5JeWmaL/xYS/Mfpc9YYrPxl52ULOpwhIuiVl9k07Yvsf9VOY+EtizSWfR6xKK6itgkvQ/+fyNs6v4XJXIsPwVL+WprCiL8AEUxw5s=)

Như ta thấy, logic lõi giữ nguyên hoàn toàn, tất cả những gì ta làm chỉ là chuyển nó vào một hàm bên ngoài và trả về phần state cần lộ ra. Cũng giống như bên trong một component, bạn có thể dùng đầy đủ [các hàm Composition API](/api/#composition-api) trong composable. Chức năng `useMouse()` này giờ có thể được dùng trong bất kỳ component nào.

Điểm hay hơn nữa của composable là bạn có thể lồng chúng vào nhau: một composable có thể gọi một hoặc nhiều composable khác. Điều này cho phép ta kết hợp logic phức tạp từ những đơn vị nhỏ, tách biệt, tương tự như cách ta kết hợp cả ứng dụng từ component. Thực ra, đây cũng chính là lý do chúng tôi quyết định đặt tên cho tập API làm nên mô hình này là Composition API.

Ví dụ, ta có thể tách logic thêm và gỡ listener sự kiện DOM thành composable riêng:

```js [event.js]
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // nếu muốn, bạn cũng có thể mở rộng composable này
  // để hỗ trợ target là chuỗi selector
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

Và giờ composable `useMouse()` có thể được đơn giản hóa thành:

```js{2,8-11} [mouse.js]
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

:::tip
Mỗi instance component gọi `useMouse()` sẽ tạo ra bản sao state `x` và `y` riêng, nên chúng sẽ không ảnh hưởng lẫn nhau. Nếu bạn muốn quản lý shared state giữa các component, hãy đọc chương [Quản lý state](/guide/scaling-up/state-management).
:::

## Ví Dụ Về Async State {#async-state-example}

Composable `useMouse()` không nhận đối số nào, vậy hãy xem một ví dụ khác có dùng đối số. Khi fetch dữ liệu bất đồng bộ, ta thường phải xử lý nhiều trạng thái khác nhau: loading, success và error:

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">Oops! Error encountered: {{ error.message }}</div>
  <div v-else-if="data">
    Data loaded:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Loading...</div>
</template>
```

Sẽ rất tẻ nhạt nếu phải lặp lại pattern này trong mọi component cần fetch dữ liệu. Hãy tách nó thành composable:

```js [fetch.js]
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

Giờ trong component ta chỉ cần làm:

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

### Chấp Nhận State Phản Ứng {#accepting-reactive-state}

`useFetch()` nhận đầu vào là một chuỗi URL tĩnh, nên nó chỉ fetch một lần rồi kết thúc. Nhưng nếu ta muốn nó fetch lại mỗi khi URL thay đổi thì sao? Để làm vậy, ta cần truyền state phản ứng vào composable, rồi để composable tạo watcher thực hiện hành động dựa trên state đó.

Ví dụ, `useFetch()` nên có thể nhận một ref:

```js
const url = ref('/initial-url')

const { data, error } = useFetch(url)

// điều này sẽ kích hoạt fetch lại
url.value = '/new-url'
```

Hoặc chấp nhận một [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description):

```js
// fetch lại khi props.id thay đổi
const { data, error } = useFetch(() => `/posts/${props.id}`)
```

Ta có thể refactor phần cài đặt hiện tại bằng API [`watchEffect()`](/api/reactivity-core.html#watcheffect) và [`toValue()`](/api/reactivity-utilities.html#tovalue):

```js{7,12} [fetch.js]
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  const fetchData = () => {
    // reset state trước khi fetch..
    data.value = null
    error.value = null

    fetch(toValue(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  watchEffect(() => {
    fetchData()
  })

  return { data, error }
}
```

`toValue()` là API được thêm vào từ 3.3. Nó được thiết kế để chuẩn hóa ref hoặc getter thành giá trị. Nếu đối số là ref, nó trả về giá trị của ref; nếu đối số là hàm, nó sẽ gọi hàm đó rồi trả về giá trị trả về. Nếu không, nó trả về nguyên đối số như cũ. Nó hoạt động tương tự [`unref()`](/api/reactivity-utilities.html#unref), nhưng có cách xử lý đặc biệt cho hàm.

Hãy chú ý rằng `toValue(url)` được gọi **bên trong** callback của `watchEffect`. Điều này bảo đảm mọi dependency phản ứng được truy cập trong quá trình chuẩn hóa `toValue()` đều được watcher theo dõi.

Phiên bản `useFetch()` này giờ chấp nhận chuỗi URL tĩnh, ref và getter, khiến nó linh hoạt hơn nhiều. Watch effect sẽ chạy ngay lập tức, đồng thời theo dõi mọi dependency được truy cập khi thực hiện `toValue(url)`. Nếu không có dependency nào được theo dõi (ví dụ URL đã là chuỗi), effect sẽ chỉ chạy một lần; ngược lại, nó sẽ chạy lại mỗi khi một dependency được theo dõi thay đổi.

Đây là [phiên bản cập nhật của `useFetch()`](https://play.vuejs.org/#eNp9Vdtu20YQ/ZUpUUA0qpAOjL4YktCbC7Rom8BN8sSHrMihtfZql9iLZEHgv2dml6SpxMiDIWkuZ+acmR2fs1+7rjgEzG6zlaut7Dw49KHbVFruO2M9nMFiu4Ta7LvgsYEeWmv2sKCkxSwoOPwTfb2b/EU5mopHR5GVro12HrbC4UerYA2Lnfeduy3LR2d0p0SNO6MatIU/dbI2DRZUtPSmMa4kgJQuG8qkjvLF28XVaAwRb2wxz69gvZkK/UQ5xUGogBQ/ZpyhEV4sAa01lnpeTwRyApsFWvT2RO6Eea40THBMgfq6NLwlS1/pVZnUJB3ph8c98fNIvwD+MaKBzkQut2xYbYP3RsPhTWvsusokSA0/Vxn8UitZP7GFSX/+8Sz7z1W2OZ9BQt+vypQXS1R+1cgDQciW4iMrimR0wu8270znfoC7SBaJWdAeLTa3QFgxuNijc+IBIy5PPyYOjU19RDEI954/Z/UptKTy6VvqA5XD1AwLTTl/0Aco4s5lV51F5sG+VJJ+v4qxYbmkfiiKYvSvyknPbJnNtoyW+HJpj4Icd22LtV+CN5/ikC4XuNL4HFPaoGsvie3FIqSJp1WIzabl00HxkoyetEVfufhv1kAu3EnX8z0CKEtKofcGzhMb2CItAELL1SPlFMV1pwVj+GROc/vWPoc26oDgdxhfSArlLnbWaBOcOoEzIP3CgbeifqLXLRyICaDBDnVD+3KC7emCSyQ4sifspOx61Hh4Qy/d8BsaOEdkYb1sZS2FoiJKnIC6FbqhsaTVZfk8gDgK6cHLPZowFGUzAQTNWl/BUSrFbzRYHXmSdeAp28RMsI0fyFDaUJg9Spd0SbERZcvZDBRleCPdQMCPh8ARwdRRnBCTjGz5WkT0i0GlSMqixTR6VKyHmmWEHIfV+naSOETyRx8vEYwMv7pa8dJU+hU9Kz2t86ReqjcgaTzCe3oGpEOeD4uyJOcjTXe+obScHwaAi82lo9dC/q/wuyINjrwbuC5uZrS4WAQeyTN9ftOXIVwy537iecoX92kR4q/F1UvqIMsSbq6vo5XF6ekCeEcTauVDFJpuQESvMv53IBXadx3r4KqMrt0w0kwoZY5/R5u3AZejvd5h/fSK/dE9s63K3vN7tQesssnnhX1An9x3//+Hz/R9cu5NExRFf8d5zyIF7jGF/RZ0Q23P4mK3f8XLRmfhg7t79qjdSIobjXLE+Cqju/b7d6i/tHtT3MQ8VrH/Ahstp5A=), có thêm độ trễ giả lập và lỗi ngẫu nhiên để làm demo.

## Quy Ước Và Thực Hành Tốt Nhất {#conventions-and-best-practices}

### Đặt Tên {#naming}

Theo quy ước, composable nên được đặt tên theo camelCase và bắt đầu bằng "use".

### Đối Số Đầu Vào {#input-arguments}

Composable có thể chấp nhận đối số là ref hoặc getter kể cả khi bản thân nó không dựa vào chúng để tạo tính phản ứng. Nếu bạn đang viết composable có thể được developer khác dùng, nên cân nhắc xử lý cả trường hợp đối số đầu vào là ref hoặc getter thay vì giá trị thô. Hàm tiện ích [`toValue()`](/api/reactivity-utilities#tovalue) rất hữu ích cho mục đích này:

```js
import { toValue } from 'vue'

function useFeature(maybeRefOrGetter) {
  // Nếu maybeRefOrGetter là ref hoặc getter,
  // giá trị đã chuẩn hóa của nó sẽ được trả về.
  // Nếu không, nó được trả về nguyên trạng.
  const value = toValue(maybeRefOrGetter)
}
```

Nếu composable của bạn tạo reactive effect khi đầu vào là ref hoặc getter, hãy bảo đảm hoặc là watch ref / getter đó một cách tường minh bằng `watch()`, hoặc gọi `toValue()` bên trong `watchEffect()` để nó được theo dõi đúng cách.

Phần cài đặt [useFetch() đã bàn ở trên](#accepting-reactive-state) là ví dụ cụ thể về composable chấp nhận ref, getter và giá trị thuần làm đầu vào.

### Giá Trị Trả Về {#return-values}

Bạn có lẽ đã nhận ra rằng trong composable ta luôn dùng `ref()` thay vì `reactive()`. Quy ước được khuyến nghị là composable nên luôn trả về một object thuần, không reactive, chứa nhiều ref. Điều này cho phép object đó được destructure trong component mà vẫn giữ tính phản ứng:

```js
// x và y là ref
const { x, y } = useMouse()
```

Nếu composable trả về object reactive, việc destructure kiểu này sẽ làm mất kết nối phản ứng tới state bên trong composable, trong khi ref thì vẫn giữ được kết nối đó.

Nếu bạn thích dùng state trả về từ composable dưới dạng property của object, bạn có thể bọc object trả về bằng `reactive()` để ref được mở bọc. Ví dụ:

```js
const mouse = reactive(useMouse())
// mouse.x được liên kết với ref gốc
console.log(mouse.x)
```

```vue-html
Mouse position is at: {{ mouse.x }}, {{ mouse.y }}
```

### Side Effect {#side-effects}

Việc thực hiện side effect (ví dụ thêm listener sự kiện DOM hoặc fetch dữ liệu) bên trong composable là hoàn toàn ổn, nhưng hãy chú ý các quy tắc sau:

- Nếu bạn đang làm việc trên ứng dụng dùng [Server-Side Rendering](/guide/scaling-up/ssr) (SSR), hãy bảo đảm các side effect liên quan tới DOM chỉ chạy trong các hook vòng đời sau mount như `onMounted()`. Những hook này chỉ được gọi trong trình duyệt, vì vậy bạn có thể chắc chắn mã bên trong chúng có quyền truy cập DOM.

- Hãy nhớ dọn dẹp side effect trong `onUnmounted()`. Ví dụ, nếu composable thiết lập một DOM event listener, nó nên gỡ listener đó trong `onUnmounted()` như ta đã thấy ở ví dụ `useMouse()`. Bạn cũng có thể cân nhắc dùng một composable hỗ trợ việc này tự động, như ví dụ `useEventListener()`.

### Giới Hạn Khi Sử Dụng {#usage-restrictions}

Composable chỉ nên được gọi trong `<script setup>` hoặc trong hook `setup()`. Chúng cũng nên được gọi **đồng bộ** trong các ngữ cảnh đó. Trong một vài trường hợp, bạn cũng có thể gọi chúng trong hook vòng đời như `onMounted()`.

Những giới hạn này rất quan trọng vì đây là các ngữ cảnh mà Vue có thể xác định instance component đang hoạt động hiện tại. Việc truy cập được vào instance đang hoạt động là cần thiết để:

1. Hook vòng đời có thể được đăng ký vào đó.

2. Watcher có thể liên kết với nó, để watcher được hủy khi instance bị unmount, tránh rò rỉ bộ nhớ.

:::tip
`<script setup>` là nơi duy nhất mà bạn có thể gọi composable **sau khi** dùng `await`. Trình biên dịch sẽ tự động khôi phục context instance đang hoạt động cho bạn sau thao tác bất đồng bộ.
:::

## Tách Composable Để Tổ Chức Mã {#extracting-composables-for-code-organization}

Composable có thể được tách ra không chỉ để tái sử dụng, mà còn để tổ chức mã. Khi độ phức tạp của component tăng lên, bạn có thể sẽ gặp phải những component quá lớn, khó điều hướng và khó suy luận. Composition API cho bạn toàn quyền linh hoạt để tổ chức mã component thành các hàm nhỏ hơn dựa trên từng mối quan tâm logic:

```vue
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

Ở một mức độ nào đó, bạn có thể xem những composable được tách ra này như các dịch vụ trong phạm vi component có thể giao tiếp với nhau.

## Dùng Composable Trong Options API {#using-composables-in-options-api}

Nếu bạn đang dùng Options API, composable phải được gọi bên trong `setup()`, và các binding trả về phải được trả về từ `setup()` để chúng được lộ ra trên `this` và template:

```js
import { useMouse } from './mouse.js'
import { useFetch } from './fetch.js'

export default {
  setup() {
    const { x, y } = useMouse()
    const { data, error } = useFetch('...')
    return { x, y, data, error }
  },
  mounted() {
    // property được lộ ra từ setup() có thể truy cập qua `this`
    console.log(this.x)
  }
  // ...các option khác
}
```

## So Sánh Với Các Kỹ Thuật Khác {#comparisons-with-other-techniques}

### So Với Mixins {#vs-mixins}

Người dùng chuyển sang từ Vue 2 có thể đã quen với option [mixins](/api/options-composition#mixins), vốn cũng cho phép ta tách logic component thành các đơn vị tái sử dụng. Mixins có ba nhược điểm chính:

1. **Nguồn gốc của property không rõ ràng**: khi dùng nhiều mixin, sẽ rất khó biết property nào của instance được đưa vào bởi mixin nào, khiến việc lần theo phần cài đặt và hiểu hành vi của component trở nên khó khăn. Đây cũng là lý do ta khuyến nghị pattern ref + destructure cho composable: nó làm rõ nguồn gốc của property ngay trong component sử dụng.

2. **Xung đột namespace**: nhiều mixin từ các tác giả khác nhau có thể đăng ký cùng một key property, gây xung đột namespace. Với composable, bạn có thể đổi tên biến khi destructure nếu có key bị trùng giữa các composable khác nhau.

3. **Giao tiếp ngầm giữa các mixin**: nhiều mixin cần tương tác với nhau sẽ phải dựa vào các key property dùng chung, khiến chúng bị gắn chặt với nhau một cách ngầm định. Với composable, giá trị trả về từ composable này có thể được truyền vào composable khác dưới dạng đối số, giống hệt hàm bình thường.

Vì các lý do trên, chúng tôi không còn khuyến nghị dùng mixin trong Vue 3 nữa. Tính năng này chỉ được giữ lại vì lý do chuyển đổi và để tương thích với mã cũ.

### So Với Renderless Component {#vs-renderless-components}

Trong chương về slot, ta đã bàn về pattern [Renderless Component](/guide/components/slots#renderless-components) dựa trên scoped slot. Ta thậm chí còn triển khai cùng demo theo dõi chuột bằng renderless component.

Ưu điểm chính của composable so với renderless component là composable không tạo ra overhead của instance component bổ sung. Khi dùng xuyên suốt toàn bộ ứng dụng, số lượng instance component thừa mà pattern renderless component tạo ra có thể trở thành một overhead hiệu năng đáng kể.

Khuyến nghị là: hãy dùng composable khi tái sử dụng logic thuần, và dùng component khi tái sử dụng cả logic lẫn bố cục hiển thị.

### So Với React Hooks {#vs-react-hooks}

Nếu bạn có kinh nghiệm với React, bạn có thể thấy điều này rất giống custom React hooks. Composition API phần nào được lấy cảm hứng từ React hooks, và composable của Vue quả thực cũng tương tự React hooks ở khả năng kết hợp logic. Tuy nhiên, composable của Vue dựa trên hệ phản ứng chi tiết của Vue, vốn khác biệt căn bản so với mô hình thực thi của React hooks. Điều này được bàn chi tiết hơn trong [Composition API FAQ](/guide/extras/composition-api-faq#comparison-with-react-hooks).

## Đọc Thêm {#further-reading}

- [Reactivity In Depth](/guide/extras/reactivity-in-depth): để hiểu ở mức thấp hệ phản ứng của Vue hoạt động ra sao.
- [State Management](/guide/scaling-up/state-management): cho các pattern quản lý state dùng chung giữa nhiều component.
- [Testing Composables](/guide/scaling-up/testing#testing-composables): các mẹo khi unit test composable.
- [VueUse](https://vueuse.org/): một bộ sưu tập composable của Vue ngày càng lớn. Mã nguồn của nó cũng là tài nguyên học tập rất tốt.
