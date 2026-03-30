---
outline: deep
---

<script setup>
import SpreadSheet from './demos/SpreadSheet.vue'
</script>

# Reactivity In-Depth {#reactivity-in-depth}

Một trong những điểm khác biệt rõ nhất của Vue là hệ thống tính phản ứng hoạt động khá "vô hình". State của component được tạo từ các object JavaScript có tính phản ứng. Khi bạn thay đổi chúng, giao diện sẽ cập nhật theo. Điều này khiến việc quản lý state trở nên đơn giản và trực quan, nhưng cũng rất đáng để hiểu rõ cơ chế bên dưới nhằm tránh những bẫy thường gặp. Trong phần này, ta sẽ đi sâu hơn vào một vài chi tiết ở mức thấp của hệ thống tính phản ứng trong Vue.

## Tính Phản Ứng Là Gì? {#what-is-reactivity}

Những năm gần đây, khái niệm này xuất hiện khá nhiều trong lập trình, nhưng khi nói "reactivity" thì người ta thực sự muốn nói gì? Tính phản ứng là một mô hình lập trình cho phép ta phản ứng với thay đổi theo cách khai báo. Ví dụ kinh điển thường được dùng để minh họa, và cũng là ví dụ rất dễ hiểu, là bảng tính Excel:

<SpreadSheet />

Ở đây ô A2 được định nghĩa bằng công thức `= A0 + A1` (bạn có thể bấm vào A2 để xem hoặc sửa công thức), nên bảng tính cho ra giá trị 3. Không có gì bất ngờ. Nhưng nếu bạn thay đổi A0 hoặc A1, bạn sẽ thấy A2 cũng tự động cập nhật theo.

JavaScript thông thường không hoạt động như vậy. Nếu ta viết thứ gì đó tương đương bằng JavaScript:

```js
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // Vẫn là 3
```

Khi ta thay đổi `A0`, `A2` không tự đổi theo.

Vậy trong JavaScript, ta sẽ làm thế nào? Trước hết, để có thể chạy lại đoạn mã cập nhật `A2`, ta hãy bọc nó vào một hàm:

```js
let A2

function update() {
  A2 = A0 + A1
}
```

Sau đó, ta cần định nghĩa vài khái niệm:

- Hàm `update()` tạo ra một **side effect**, hay gọi ngắn là **effect**, vì nó thay đổi state của chương trình.

- `A0` và `A1` được xem là **dependency** của effect, vì giá trị của chúng được dùng để thực hiện effect. Ta nói effect là một **subscriber** của các dependency đó.

Điều ta cần là một hàm "ma thuật" có thể gọi `update()` (tức **effect**) bất cứ khi nào `A0` hoặc `A1` (tức **dependency**) thay đổi:

```js
whenDepsChange(update)
```

Hàm `whenDepsChange()` này cần làm các việc sau:

1. Theo dõi khi một biến được đọc. Ví dụ khi biểu thức `A0 + A1` được đánh giá, cả `A0` lẫn `A1` đều được đọc.

2. Nếu một biến được đọc trong khi có một effect đang chạy, đăng ký effect đó làm subscriber của biến. Ví dụ vì `A0` và `A1` được đọc khi `update()` đang chạy, nên sau lần gọi đầu tiên `update()` sẽ trở thành subscriber của cả `A0` và `A1`.

3. Phát hiện khi một biến bị thay đổi. Ví dụ khi `A0` được gán giá trị mới, thông báo cho mọi effect đang subscribe nó chạy lại.

## Vue Làm Tính Phản Ứng Như Thế Nào {#how-reactivity-works-in-vue}

Ta không thể thực sự theo dõi việc đọc và ghi lên biến cục bộ như ví dụ trên. JavaScript thuần đơn giản là không có cơ chế cho việc đó. Tuy nhiên, thứ ta **có thể** làm là chặn việc đọc và ghi trên **property của object**.

Trong JavaScript có hai cách để chặn truy cập property: [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) / [setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description) và [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Vue 2 chỉ dùng getter / setter do hạn chế hỗ trợ trình duyệt. Sang Vue 3, Proxy được dùng cho object reactive còn getter / setter được dùng cho ref. Đây là một đoạn pseudo-code minh họa:

```js{4,9,17,22}
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key)
      return target[key]
    },
    set(target, key, value) {
      target[key] = value
      trigger(target, key)
    }
  })
}

function ref(value) {
  const refObject = {
    get value() {
      track(refObject, 'value')
      return value
    },
    set value(newValue) {
      value = newValue
      trigger(refObject, 'value')
    }
  }
  return refObject
}
```

:::tip
Các đoạn mã ở đây và bên dưới chỉ nhằm giải thích khái niệm cốt lõi theo cách đơn giản nhất có thể, nên nhiều chi tiết và edge case đã được lược bỏ.
:::

Điều này cũng giải thích một vài [giới hạn của object reactive](/guide/essentials/reactivity-fundamentals#limitations-of-reactive) mà ta đã nhắc đến ở phần nền tảng:

- Khi bạn gán hoặc destructure một property của object reactive vào biến cục bộ, việc truy cập hoặc gán lên biến đó sẽ không còn reactive nữa vì nó không còn kích hoạt bẫy get / set trên object nguồn. Lưu ý sự "ngắt kết nối" này chỉ ảnh hưởng đến binding của biến. Nếu biến đó trỏ đến một giá trị không nguyên thủy như object, việc thay đổi object đó vẫn reactive.

- Proxy được trả về từ `reactive()`, dù hoạt động giống object gốc, vẫn có định danh khác nếu so sánh với object gốc bằng toán tử `===`.

Bên trong `track()`, ta kiểm tra xem hiện có effect nào đang chạy không. Nếu có, ta tra ra tập các effect đang subscribe property đang được theo dõi (được lưu trong một `Set`) rồi thêm effect đó vào:

```js
// Biến này sẽ được gán ngay trước khi một effect
// chuẩn bị được chạy. Ta sẽ quay lại phần này sau.
let activeEffect

function track(target, key) {
  if (activeEffect) {
    const effects = getSubscribersForProperty(target, key)
    effects.add(activeEffect)
  }
}
```

Các đăng ký effect được lưu trong một cấu trúc toàn cục `WeakMap<target, Map<key, Set<effect>>>`. Nếu chưa có `Set` subscriber nào cho property đó (tức là property được track lần đầu), nó sẽ được tạo ra. Về cơ bản, đó là việc mà `getSubscribersForProperty()` làm. Để đơn giản, ta sẽ bỏ qua chi tiết cài đặt của nó.

Bên trong `trigger()`, ta lại tra ra các effect subscribe property đó. Lần này, thay vì thêm vào, ta gọi chúng:

```js
function trigger(target, key) {
  const effects = getSubscribersForProperty(target, key)
  effects.forEach((effect) => effect())
}
```

Giờ hãy quay lại hàm `whenDepsChange()`:

```js
function whenDepsChange(update) {
  const effect = () => {
    activeEffect = effect
    update()
    activeEffect = null
  }
  effect()
}
```

Nó bọc hàm `update` gốc vào trong một effect. Effect này tự đặt chính nó làm effect đang hoạt động trước khi chạy phần cập nhật thật. Nhờ đó, các lệnh gọi `track()` trong quá trình cập nhật sẽ biết effect hiện tại là gì.

Đến đây, ta đã tạo ra được một effect có thể tự động theo dõi dependency của chính nó, rồi chạy lại bất cứ khi nào một dependency thay đổi. Ta gọi đó là **Reactive Effect**.

Vue cung cấp API để tạo reactive effect là [`watchEffect()`](/api/reactivity-core#watcheffect). Thực tế, bạn có thể nhận ra nó hoạt động khá giống hàm `whenDepsChange()` "ma thuật" trong ví dụ ở trên. Giờ ta có thể viết lại ví dụ ban đầu bằng API Vue thật:

```js
import { ref, watchEffect } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = ref()

watchEffect(() => {
  // theo dõi A0 và A1
  A2.value = A0.value + A1.value
})

// kích hoạt effect
A0.value = 2
```

Dùng reactive effect để thay đổi một ref không phải trường hợp thú vị nhất. Thực tế, dùng computed sẽ mang tính khai báo hơn:

```js
import { ref, computed } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = computed(() => A0.value + A1.value)

A0.value = 2
```

Bên trong, `computed` quản lý việc vô hiệu hóa và tính toán lại thông qua một reactive effect.

Vậy một ví dụ thực tế và hữu ích hơn của reactive effect là gì? Cập nhật DOM. Ta có thể hiện thực một kiểu "reactive rendering" rất đơn giản như sau:

```js
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  document.body.innerHTML = `Count is: ${count.value}`
})

// cập nhật DOM
count.value++
```

Thực ra, điều này khá gần với cách một component Vue đồng bộ state và DOM: mỗi instance component tạo ra một reactive effect để render và cập nhật DOM. Dĩ nhiên, Vue dùng những cách hiệu quả hơn nhiều so với `innerHTML`. Điều này được giải thích ở phần [Cơ Chế Render](./rendering-mechanism).

<div class="options-api">

Các API `ref()`, `computed()` và `watchEffect()` đều thuộc Composition API. Nếu từ trước đến nay bạn chỉ dùng Options API, bạn sẽ nhận ra Composition API gần với cách hệ thống tính phản ứng của Vue vận hành bên dưới hơn. Trên thực tế, trong Vue 3, Options API được xây dựng trên nền Composition API. Mọi lần truy cập property trên instance component (`this`) đều đi qua getter / setter để theo dõi phản ứng, còn các option như `watch` và `computed` thì bên trong sẽ gọi các phiên bản Composition API tương đương.

</div>

## Tính Phản Ứng Runtime Và Compile-time {#runtime-vs-compile-time-reactivity}

Hệ thống tính phản ứng của Vue chủ yếu hoạt động ở runtime: việc track và trigger đều được thực hiện khi mã đang chạy trực tiếp trong trình duyệt. Ưu điểm của tính phản ứng runtime là nó có thể hoạt động mà không cần bước build và có ít edge case hơn. Mặt khác, nó bị giới hạn bởi cú pháp JavaScript, nên ta cần đến những value container như ref của Vue.

Một số framework như [Svelte](https://svelte.dev/) lại chọn vượt qua giới hạn này bằng cách triển khai tính phản ứng ở thời điểm biên dịch. Chúng phân tích và biến đổi mã để mô phỏng tính phản ứng. Bước biên dịch cho phép framework thay đổi chính ngữ nghĩa của JavaScript, ví dụ tự động chèn mã để phân tích dependency và trigger effect quanh việc truy cập biến cục bộ. Đổi lại, cách này yêu cầu build step, và việc thay đổi ngữ nghĩa JavaScript về bản chất là tạo ra một ngôn ngữ trông giống JavaScript nhưng biên dịch thành thứ khác.

Đội ngũ Vue cũng từng thử hướng đi này qua tính năng thử nghiệm [Reactivity Transform](/guide/extras/reactivity-transform), nhưng cuối cùng chúng tôi quyết định đây không phải lựa chọn phù hợp cho dự án vì [những lý do được giải thích tại đây](https://github.com/vuejs/rfcs/discussions/369#discussioncomment-5059028).

## Debug Tính Phản Ứng {#reactivity-debugging}

Việc hệ phản ứng của Vue tự động theo dõi dependency là rất tiện, nhưng trong một số trường hợp ta muốn biết chính xác thứ gì đang được theo dõi, hoặc điều gì đang khiến component render lại.

### Hook Debug Component {#component-debugging-hooks}

Ta có thể debug xem những dependency nào được dùng trong quá trình render của component, và dependency nào đang kích hoạt một lần cập nhật, bằng các hook vòng đời <span class="options-api">`renderTracked`</span><span class="composition-api">`onRenderTracked`</span> và <span class="options-api">`renderTriggered`</span><span class="composition-api">`onRenderTriggered`</span>. Cả hai hook đều nhận một debugger event chứa thông tin về dependency liên quan. Cách dùng được khuyến nghị là đặt một câu lệnh `debugger` bên trong callback để kiểm tra tương tác:

<div class="composition-api">

```vue
<script setup>
import { onRenderTracked, onRenderTriggered } from 'vue'

onRenderTracked((event) => {
  debugger
})

onRenderTriggered((event) => {
  debugger
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  renderTracked(event) {
    debugger
  },
  renderTriggered(event) {
    debugger
  }
}
```

</div>

:::tip
Các hook debug component chỉ hoạt động trong chế độ phát triển.
:::

Object debug event có kiểu như sau:

<span id="debugger-event"></span>

```ts
type DebuggerEvent = {
  effect: ReactiveEffect
  target: object
  type:
    | TrackOpTypes /* 'get' | 'has' | 'iterate' */
    | TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
  key: any
  newValue?: any
  oldValue?: any
  oldTarget?: Map<any, any> | Set<any>
}
```

### Debug Computed {#computed-debugging}

<!-- TODO options API equivalent -->

Ta có thể debug computed bằng cách truyền đối số thứ hai cho `computed()` là một object option có callback `onTrack` và `onTrigger`:

- `onTrack` sẽ được gọi khi một property reactive hoặc ref được track làm dependency.
- `onTrigger` sẽ được gọi khi callback của watcher bị kích hoạt bởi việc thay đổi một dependency.

Cả hai callback đều nhận debugger event có [cùng định dạng](#debugger-event) với hook debug component:

```js
const plusOne = computed(() => count.value + 1, {
  onTrack(e) {
    // được gọi khi count.value được track làm dependency
    debugger
  },
  onTrigger(e) {
    // được gọi khi count.value bị thay đổi
    debugger
  }
})

// truy cập plusOne, sẽ kích hoạt onTrack
console.log(plusOne.value)

// thay đổi count.value, sẽ kích hoạt onTrigger
count.value++
```

:::tip
Option `onTrack` và `onTrigger` của computed chỉ hoạt động trong chế độ phát triển.
:::

### Debug Watcher {#watcher-debugging}

<!-- TODO options API equivalent -->

Tương tự `computed()`, watcher cũng hỗ trợ option `onTrack` và `onTrigger`:

```js
watch(source, callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})

watchEffect(callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})
```

:::tip
Option `onTrack` và `onTrigger` của watcher chỉ hoạt động trong chế độ phát triển.
:::

## Tích Hợp Với Hệ Thống State Bên Ngoài {#integration-with-external-state-systems}

Hệ thống tính phản ứng của Vue hoạt động bằng cách chuyển sâu các object JavaScript thuần thành proxy reactive. Việc chuyển sâu này đôi khi không cần thiết hoặc không mong muốn khi tích hợp với các hệ quản lý state bên ngoài, ví dụ khi giải pháp bên ngoài cũng dùng Proxy.

Ý tưởng chung khi tích hợp hệ thống tính phản ứng của Vue với một giải pháp quản lý state bên ngoài là giữ state bên ngoài trong [`shallowRef`](/api/reactivity-advanced#shallowref). Shallow ref chỉ reactive khi property `.value` của nó được truy cập, còn giá trị bên trong thì được giữ nguyên. Khi state bên ngoài thay đổi, chỉ cần thay giá trị ref để kích hoạt cập nhật.

### Dữ Liệu Bất Biến {#immutable-data}

Nếu bạn đang triển khai tính năng undo / redo, rất có thể bạn sẽ muốn chụp snapshot state của ứng dụng sau mỗi lần người dùng chỉnh sửa. Tuy nhiên, hệ phản ứng có thể thay đổi trực tiếp của Vue không thật sự phù hợp nếu cây state lớn, vì việc serialize toàn bộ object state sau mỗi lần cập nhật có thể rất tốn CPU lẫn bộ nhớ.

[Cấu trúc dữ liệu bất biến](https://en.wikipedia.org/wiki/Persistent_data_structure) giải quyết vấn đề này bằng cách không bao giờ thay đổi trực tiếp object state. Thay vào đó, nó tạo object mới nhưng dùng chung các phần không đổi với object cũ. Có nhiều cách dùng dữ liệu bất biến trong JavaScript, nhưng chúng tôi khuyên dùng [Immer](https://immerjs.github.io/immer/) với Vue vì nó cho phép bạn dùng dữ liệu bất biến trong khi vẫn giữ được cú pháp mutate quen thuộc và tiện hơn.

Ta có thể tích hợp Immer với Vue thông qua một composable đơn giản:

```js
import { produce } from 'immer'
import { shallowRef } from 'vue'

export function useImmer(baseState) {
  const state = shallowRef(baseState)
  const update = (updater) => {
    state.value = produce(state.value, updater)
  }

  return [state, update]
}
```

[Thử trên Playground](https://play.vuejs.org/#eNp9VMFu2zAM/RXNl6ZAYnfoTlnSdRt66DBsQ7vtEuXg2YyjRpYEUU5TBPn3UZLtuE1RH2KLfCIfycfsk8/GpNsGkmkyw8IK4xiCa8wVV6I22jq2Zw3CbV2DZQe2srpmZ2km/PmMK8a4KrRCxxbCQY1j1pgyd3DrD0s27++OFh689z/0OOEkTBlPvkNuFfvbAE/Gra/UilzOko0Mh2A+ufcHwd9ij8KtWUjwMsAqlxgjcLU854qrVaMKJ7RiTleVDBRHQpWwO4/xB8xHoRg2v+oyh/MioJepT0ClvTsxhnSUi1LOsthN6iMdCGgkBacTY7NGhjd9ScG2k5W2c56M9rG6ceBPdbOWm1AxO0/a+uiZFjJHpFv7Fj10XhdSFBtyntTJkzaxf/ZtQnYguoFNJkUkmAWGs2xAm47onqT/jPWHxjjYuUkJhba57+yUSaFg4tZWN9X6Y9eIcC8ZJ1FQkzo36QNqRZILQXjroAqnXb+9LQzVD3vtnMFpljXKbKq00HWU3/X7i/QivcxKgS5aUglVXjxNAGvK8KnWZSNJWa0KDoGChzmk3L28jSVcQX1o1d1puwfgOpdSP97BqsfQxhCCK9gFTC+tXu7/coR7R71rxRWXBL2FpHOMOAAeYVGJhBvFL3s+kGKIkW5zSfKfd+RHA2u3gzZEpML9y9JS06YtAq5DLFmOMWXsjkM6rET1YjzUcSMk2J/G1/h8TKGOb8HmV7bdQbqzhmLziv0Bd3Govywg2O1x8Umvua3ARffN/Q/S1sDZDfMN5x2glo3nGGFfGlUS7QEusL0NcxWq+o03OwcKu6Ke/+fwhIb89Y3Sj3Qv0w+9xg7/AWfvyMs=)

### State Machine {#state-machines}

[State Machine](https://en.wikipedia.org/wiki/Finite-state_machine) là mô hình mô tả toàn bộ các trạng thái có thể có của một ứng dụng và mọi cách ứng dụng có thể chuyển từ trạng thái này sang trạng thái khác. Với những component đơn giản thì cách này có thể là quá mức cần thiết, nhưng nó giúp những luồng state phức tạp trở nên chắc chắn và dễ quản lý hơn.

Một trong những triển khai state machine phổ biến nhất trong JavaScript là [XState](https://xstate.js.org/). Đây là một composable tích hợp với nó:

```js
import { createMachine, interpret } from 'xstate'
import { shallowRef } from 'vue'

export function useMachine(options) {
  const machine = createMachine(options)
  const state = shallowRef(machine.initialState)
  const service = interpret(machine)
    .onTransition((newState) => (state.value = newState))
    .start()
  const send = (event) => service.send(event)

  return [state, send]
}
```

[Thử trên Playground](https://play.vuejs.org/#eNp1U81unDAQfpWRL7DSFqqqUiXEJumhyqVVpDa3ugcKZtcJjC1syEqId8/YBu/uIRcEM9/P/DGz71pn0yhYwUpTD1JbMMKO+o6j7LUaLMwwGvGrqk8SBSzQDqqHJMv7EMleTMIRgGOt0Fj4a2xlxZ5EsPkHhytuOjucbApIrDoeO5HsfQCllVVHUYlVbeW0xr2OKcCzHCwkKQAK3fP56fHx5w/irSyqbfFMgA+h0cKBHZYey45jmYfeqWv6sKLXHbnTF0D5f7RWITzUnaxfD5y5ztIkSCY7zjwKYJ5DyVlf2fokTMrZ5sbZDu6Bs6e25QwK94b0svgKyjwYkEyZR2e2Z2H8n/pK04wV0oL8KEjWJwxncTicnb23C3F2slabIs9H1K/HrFZ9HrIPX7Mv37LPuTC5xEacSfa+V83YEW+bBfleFkuW8QbqQZDEuso9rcOKQQ/CxosIHnQLkWJOVdept9+ijSA6NEJwFGePaUekAdFwr65EaRcxu9BbOKq1JDqnmzIi9oL0RRDu4p1u/ayH9schrhlimGTtOLGnjeJRAJnC56FCQ3SFaYriLWjA4Q7SsPOp6kYnEXMbldKDTW/ssCFgKiaB1kusBWT+rkLYjQiAKhkHvP2j3IqWd5iMQ+M=)

### RxJS {#rxjs}

[RxJS](https://rxjs.dev/) là một thư viện làm việc với các luồng sự kiện bất đồng bộ. Thư viện [VueUse](https://vueuse.org/) cung cấp add-on [`@vueuse/rxjs`](https://vueuse.org/rxjs/readme.html) để kết nối stream RxJS với hệ thống tính phản ứng của Vue.

## Liên Hệ Với Signals {#connection-to-signals}

Khá nhiều framework khác đã giới thiệu các primitive phản ứng tương tự ref của Composition API trong Vue, dưới tên gọi "signals":

- [Solid Signals](https://docs.solidjs.com/concepts/signals)
- [Angular Signals](https://angular.dev/guide/signals)
- [Preact Signals](https://preactjs.com/guide/v10/signals/)
- [Qwik Signals](https://qwik.builder.io/docs/components/state/#usesignal)

Về bản chất, signals là cùng một loại primitive phản ứng như ref của Vue. Nó là một value container có khả năng theo dõi dependency khi truy cập và kích hoạt side effect khi thay đổi. Mô hình dựa trên primitive phản ứng này thực ra không phải khái niệm mới trong frontend. Nó đã xuất hiện từ hơn một thập kỷ trước với những triển khai như [Knockout observables](https://knockoutjs.com/documentation/observables.html) và [Meteor Tracker](https://docs.meteor.com/api/tracker.html). Options API của Vue và thư viện quản lý state [MobX](https://mobx.js.org/) của React cũng dựa trên cùng nguyên lý đó, chỉ là chúng che primitive đi sau property của object.

Dù không phải điều kiện bắt buộc để được gọi là signals, ngày nay khái niệm này thường đi kèm với mô hình render dựa trên các subscription chi tiết. Do sử dụng virtual DOM, hiện tại Vue [dựa vào compiler để đạt được những tối ưu tương tự](/guide/extras/rendering-mechanism#compiler-informed-virtual-dom). Tuy vậy, chúng tôi cũng đang thử nghiệm một chiến lược biên dịch mới lấy cảm hứng từ Solid, gọi là [Vapor Mode](https://github.com/vuejs/core-vapor), không dựa trên virtual DOM và tận dụng nhiều hơn hệ phản ứng sẵn có của Vue.

### Đánh Đổi Trong Thiết Kế API {#api-design-trade-offs}

Thiết kế signals của Preact và Qwik khá giống [shallowRef](/api/reactivity-advanced#shallowref) của Vue: cả ba đều cung cấp giao diện có thể thay đổi qua property `.value`. Ở đây, ta sẽ tập trung vào signals của Solid và Angular.

#### Solid Signals {#solid-signals}

Thiết kế API `createSignal()` của Solid nhấn mạnh việc tách biệt đọc và ghi. Signal được lộ ra dưới dạng một getter chỉ đọc và một setter riêng:

```js
const [count, setCount] = createSignal(0)

count() // truy cập giá trị
setCount(1) // cập nhật giá trị
```

Hãy để ý rằng signal `count` có thể được truyền xuống dưới mà không kèm setter. Nhờ đó state không thể bị thay đổi trừ khi setter cũng được lộ ra một cách tường minh. Việc sự an toàn này có xứng đáng với cú pháp dài hơn hay không còn tùy yêu cầu dự án và sở thích cá nhân. Nhưng nếu bạn thích kiểu API này, bạn có thể mô phỏng nó trong Vue khá dễ:

```js
import { shallowRef, triggerRef } from 'vue'

export function createSignal(value, options) {
  const r = shallowRef(value)
  const get = () => r.value
  const set = (v) => {
    r.value = typeof v === 'function' ? v(r.value) : v
    if (options?.equals === false) triggerRef(r)
  }
  return [get, set]
}
```

[Thử trên Playground](https://play.vuejs.org/#eNpdUk1TgzAQ/Ss7uQAjgr12oNXxH+ix9IAYaDQkMV/qMPx3N6G0Uy9Msu/tvn2PTORJqcI7SrakMp1myoKh1qldI9iopLYwQadpa+krG0TLYYZeyxGSojSSs/d7E8vFh0ka0YhOCmPh0EknbB4mPYfTEeqbIelD1oiqXPRQCS+WjoojAW8A1Wmzm1A39KYZzHNVYiUib85aKeCx46z7rBuySqQe6h14uINN1pDIBWACVUcqbGwtl17EqvIiR3LyzwcmcXFuTi3n8vuF9jlYzYaBajxfMsDcomv6E/m9E51luN2NV99yR3OQKkAmgykss+SkMZerxMLEZFZ4oBYJGAA600VEryAaD6CPaJwJKwnr9ldR2WMedV1Dsi6WwB58emZlsAV/zqmH9LzfvqBfruUmNvZ4QN7VearjenP4aHwmWsABt4x/+tiImcx/z27Jqw==)

#### Angular Signals {#angular-signals}

Angular đang trải qua những thay đổi nền tảng khi rời bỏ cơ chế dirty-checking và đưa vào triển khai primitive phản ứng riêng. API Signal của Angular trông như sau:

```js
const count = signal(0)

count() // truy cập giá trị
count.set(1) // gán giá trị mới
count.update((v) => v + 1) // cập nhật dựa trên giá trị trước đó
```

Một lần nữa, ta cũng có thể mô phỏng API này trong Vue khá dễ:

```js
import { shallowRef } from 'vue'

export function signal(initialValue) {
  const r = shallowRef(initialValue)
  const s = () => r.value
  s.set = (value) => {
    r.value = value
  }
  s.update = (updater) => {
    r.value = updater(r.value)
  }
  return s
}
```

[Thử trên Playground](https://play.vuejs.org/#eNp9Ul1v0zAU/SuWX9ZCSRh7m9IKGHuAB0AD8WQJZclt6s2xLX+ESlH+O9d2krbr1Df7nnPu17k9/aR11nmgt7SwleHaEQvO6w2TvNXKONITyxtZihWpVKu9g5oMZGtUS66yvJSNF6V5lyjZk71ikslKSeuQ7qUj61G+eL+cgFr5RwGITAkXiyVZb5IAn2/IB+QWeeoHO8GPg1aL0gH+CCl215u7mJ3bW9L3s3IYihyxifMlFRpJqewL1qN3TknysRK8el4zGjNlXtdYa9GFrjryllwvGY18QrisDLQgXZTnSX8pF64zzD7pDWDghbbI5/Hoip7tFL05eLErhVD/HmB75Edpyd8zc9DUaAbso3TrZeU4tjfawSV3vBR/SuFhSfrQUXLHBMvmKqe8A8siK7lmsi5gAbJhWARiIGD9hM7BIfHSgjGaHljzlDyGF2MEPQs6g5dpcAIm8Xs+2XxODTgUn0xVYdJ5RxPhKOd4gdMsA/rgLEq3vEEHlEQPYrbgaqu5APNDh6KWUTyuZC2jcWvfYswZD6spXu2gen4l/mT3Icboz3AWpgNGZ8yVBttM8P2v77DH9wy2qvYC2RfAB7BK+NBjon32ssa2j3ix26/xsrhsftv7vQNpp6FCo4E5RD6jeE93F0Y/tHuT3URd2OLwHyXleRY=)

So với ref của Vue, kiểu API dựa trên getter của Solid và Angular mang đến một vài đánh đổi đáng chú ý khi dùng trong component Vue:

- `()` ngắn gọn hơn `.value` một chút, nhưng khi cập nhật giá trị thì dài hơn.
- Không có cơ chế mở bọc ref: việc truy cập giá trị luôn cần `()`. Điều này khiến cách truy cập giá trị nhất quán ở mọi nơi. Đồng thời, bạn cũng có thể truyền raw signal xuống như prop của component.

Việc những kiểu API này có phù hợp với bạn hay không phần nào mang tính chủ quan. Mục tiêu ở đây là chỉ ra sự tương đồng nền tảng và các đánh đổi giữa những thiết kế API khác nhau. Chúng tôi cũng muốn cho thấy Vue rất linh hoạt: bạn không hề bị khóa cứng vào API hiện tại. Nếu cần, bạn hoàn toàn có thể tự tạo ra API primitive phản ứng phù hợp hơn với nhu cầu cụ thể của mình.
