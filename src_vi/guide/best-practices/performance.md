---
outline: deep
---

# Performance {#performance}

## Tổng quan {#overview}

Vue được thiết kế để có hiệu năng tốt trong hầu hết các trường hợp phổ biến mà không cần tối ưu thủ công quá nhiều. Tuy vậy, vẫn luôn có những tình huống khó hơn đòi hỏi phải tinh chỉnh thêm. Trong phần này, ta sẽ bàn về những điểm cần chú ý khi nói tới hiệu năng trong ứng dụng Vue.

Trước hết, hãy nói về hai mặt chính của hiệu năng web:

- **Hiệu năng tải trang**: ứng dụng hiển thị nội dung và trở nên tương tác được nhanh đến đâu ở lần truy cập đầu tiên. Phần này thường được đo bằng các chỉ số web vital như [Largest Contentful Paint (LCP)](https://web.dev/lcp/) và [Interaction to Next Paint](https://web.dev/articles/inp).
- **Hiệu năng cập nhật**: ứng dụng phản hồi với thao tác người dùng nhanh đến đâu. Ví dụ, danh sách cập nhật nhanh ra sao khi người dùng gõ vào ô tìm kiếm, hoặc trang chuyển nhanh tới mức nào khi người dùng bấm vào một liên kết điều hướng trong một Single-Page Application (SPA).

Lý tưởng nhất là tối ưu được cả hai, nhưng mỗi kiểu kiến trúc frontend lại ảnh hưởng khác nhau đến từng mặt. Ngoài ra, loại ứng dụng bạn đang xây cũng ảnh hưởng lớn đến việc nên ưu tiên tối ưu cái gì. Bước đầu tiên để có hiệu năng tốt là chọn đúng kiến trúc cho loại ứng dụng của bạn:

- Xem [Ways of Using Vue](/guide/extras/ways-of-using-vue) để hiểu những cách khác nhau mà bạn có thể tận dụng Vue.
- Jason Miller bàn về các loại ứng dụng web và cách triển khai / phân phối phù hợp nhất của chúng trong bài [Application Holotypes](https://jasonformat.com/application-holotypes/).

## Công cụ đo hiệu năng {#profiling-options}

Để cải thiện hiệu năng, trước hết ta cần biết cách đo nó. Có khá nhiều công cụ tốt có thể giúp việc này:

Để đo hiệu năng tải trang của bản production:

- [PageSpeed Insights](https://pagespeed.web.dev/)
- [WebPageTest](https://www.webpagetest.org/)

Để đo hiệu năng trong lúc phát triển cục bộ:

- [Chrome DevTools Performance Panel](https://developer.chrome.com/docs/devtools/evaluate-performance/)
  - [`app.config.performance`](/api/application#app-config-performance) bật các marker hiệu năng riêng của Vue trong timeline hiệu năng của Chrome DevTools.
- [Vue DevTools Extension](/guide/scaling-up/tooling#browser-devtools) cũng có tính năng đo hiệu năng.

## Tối ưu hiệu năng tải trang {#page-load-optimizations}

Có nhiều khía cạnh không phụ thuộc framework để tối ưu hiệu năng tải trang. Hãy xem [hướng dẫn này trên web.dev](https://web.dev/fast/) để có cái nhìn tổng quát. Ở đây, ta sẽ chủ yếu tập trung vào những kỹ thuật riêng cho Vue.

### Chọn đúng kiến trúc {#choosing-the-right-architecture}

Nếu trường hợp sử dụng của bạn nhạy với hiệu năng tải trang, hãy tránh đóng gói toàn bộ dưới dạng SPA chạy hoàn toàn phía client. Bạn nên để server gửi thẳng HTML có chứa nội dung mà người dùng cần xem. Việc render hoàn toàn phía client thường có thời gian hiển thị nội dung đầu tiên chậm hơn. Điều này có thể được cải thiện bằng [Server-Side Rendering (SSR)](/guide/extras/ways-of-using-vue#fullstack-ssr) hoặc [Static Site Generation (SSG)](/guide/extras/ways-of-using-vue#jamstack-ssg). Hãy xem [SSR Guide](/guide/scaling-up/ssr) để tìm hiểu cách làm SSR với Vue. Nếu ứng dụng của bạn không đòi hỏi tương tác phong phú, bạn cũng có thể dùng một backend truyền thống để render HTML và chỉ tăng cường thêm tương tác bằng Vue ở phía client.

Nếu phần chính của ứng dụng bắt buộc phải là SPA, nhưng bạn có thêm các trang marketing như landing page, trang giới thiệu, blog, thì hãy tách chúng ra riêng. Lý tưởng nhất là các trang marketing nên được triển khai dưới dạng HTML tĩnh với lượng JavaScript tối thiểu bằng cách dùng SSG.

### Kích thước bundle và tree-shaking {#bundle-size-and-tree-shaking}

Một trong những cách hiệu quả nhất để cải thiện hiệu năng tải trang là gửi đi bundle JavaScript nhỏ hơn. Dưới đây là một vài cách để giảm kích thước bundle khi dùng Vue:

- Dùng bước build nếu có thể.

  - Nhiều API của Vue có thể [tree-shakable](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking) nếu được bundle bằng build tool hiện đại. Ví dụ, nếu bạn không dùng component `<Transition>` có sẵn, nó sẽ không bị đưa vào bundle production cuối cùng. Tree-shaking cũng có thể loại bỏ các module không dùng trong source code của bạn.
  - Khi dùng bước build, template sẽ được biên dịch trước nên ta không cần gửi Vue compiler lên trình duyệt. Cách này tiết kiệm **14kb** JavaScript sau minify + gzip và tránh được chi phí compile lúc chạy.

- Cẩn thận với kích thước khi thêm dependency mới. Trong ứng dụng thực tế, bundle phình to thường là vì đưa vào dependency nặng mà không để ý.

  - Nếu có dùng bước build, hãy ưu tiên dependency có định dạng ES module và thân thiện với tree-shaking. Ví dụ, ưu tiên `lodash-es` thay vì `lodash`.
  - Kiểm tra kích thước của dependency và cân nhắc xem chức năng mà nó mang lại có đáng hay không. Ngay cả khi dependency hỗ trợ tree-shaking, mức tăng kích thước thực tế vẫn phụ thuộc vào những API bạn thật sự import từ nó. Những công cụ như [bundlejs.com](https://bundlejs.com/) có thể giúp kiểm tra nhanh, nhưng đo trực tiếp với thiết lập build thật của bạn vẫn luôn chính xác nhất.

- Nếu bạn dùng Vue chủ yếu để progressive enhancement và muốn tránh bước build, hãy cân nhắc [petite-vue](https://github.com/vuejs/petite-vue) (chỉ **6kb**).

### Code splitting {#code-splitting}

Code splitting là kỹ thuật để build tool tách bundle của ứng dụng thành nhiều chunk nhỏ hơn, từ đó có thể tải theo nhu cầu hoặc tải song song. Nếu tách tốt, những tính năng cần ngay khi vào trang sẽ được tải trước, còn các chunk bổ sung chỉ được lazy load khi thật sự cần, nhờ vậy cải thiện hiệu năng.

Các bundler như Rollup, vốn là nền tảng của Vite, hoặc webpack có thể tự động tạo split chunk bằng cách nhận diện cú pháp dynamic import của ESM:

```js
// lazy.js và các dependency của nó sẽ được tách thành chunk riêng
// và chỉ được tải khi `loadLazy()` được gọi.
function loadLazy() {
  return import('./lazy.js')
}
```

Lazy loading phù hợp nhất với những tính năng không cần ngay sau lần tải trang đầu tiên. Trong ứng dụng Vue, ta có thể kết hợp kỹ thuật này với tính năng [Async Component](/guide/components/async) của Vue để tạo chunk riêng cho từng cây component:

```js
import { defineAsyncComponent } from 'vue'

// một chunk riêng sẽ được tạo cho Foo.vue và các dependency của nó.
// nó chỉ được tải khi cần, khi async component này
// được render trên trang.
const Foo = defineAsyncComponent(() => import('./Foo.vue'))
```

Với ứng dụng dùng Vue Router, rất nên dùng lazy loading cho component của route. Vue Router hỗ trợ sẵn việc này, tách biệt với `defineAsyncComponent`. Xem [Lazy Loading Routes](https://router.vuejs.org/guide/advanced/lazy-loading.html) để biết thêm chi tiết.

## Tối ưu hiệu năng cập nhật {#update-optimizations}

### Độ ổn định của props {#props-stability}

Trong Vue, một component con chỉ cập nhật khi ít nhất một prop mà nó nhận được thay đổi. Hãy xem ví dụ sau:

```vue-html
<ListItem
  v-for="item in list"
  :id="item.id"
  :active-id="activeId" />
```

Bên trong component `<ListItem>`, nó dùng `id` và `activeId` để xác định xem có phải item hiện đang active hay không. Cách này chạy được, nhưng vấn đề là mỗi khi `activeId` thay đổi, **mọi** `<ListItem>` trong danh sách đều phải cập nhật.

Lý tưởng nhất là chỉ những item có trạng thái active thay đổi mới cần cập nhật. Ta có thể làm vậy bằng cách chuyển phần tính toán trạng thái active lên component cha, rồi để `<ListItem>` nhận trực tiếp một prop `active`:

```vue-html
<ListItem
  v-for="item in list"
  :id="item.id"
  :active="item.id === activeId" />
```

Giờ đây, với đa số component, prop `active` sẽ không đổi khi `activeId` thay đổi, nên chúng không còn cần cập nhật nữa. Nói chung, ý tưởng là giữ cho các prop truyền xuống component con ổn định nhất có thể.

### `v-once` {#v-once}

`v-once` là một directive có sẵn, dùng để render nội dung phụ thuộc vào dữ liệu lúc chạy nhưng sau đó không bao giờ cần cập nhật nữa. Toàn bộ cây con mà nó được gắn vào sẽ bị bỏ qua trong mọi lần cập nhật về sau. Xem [API reference](/api/built-in-directives#v-once) để biết thêm chi tiết.

### `v-memo` {#v-memo}

`v-memo` là một directive có sẵn, dùng để bỏ qua có điều kiện việc cập nhật những cây con lớn hoặc các danh sách `v-for`. Xem [API reference](/api/built-in-directives#v-memo) để biết thêm chi tiết.

### Độ ổn định của computed {#computed-stability}

Từ Vue 3.4 trở lên, một thuộc tính computed chỉ kích hoạt effect khi giá trị tính ra của nó đã thay đổi so với lần trước. Ví dụ, computed `isEven` dưới đây chỉ kích hoạt effect nếu giá trị trả về đổi từ `true` sang `false`, hoặc ngược lại:

```js
const count = ref(0)
const isEven = computed(() => count.value % 2 === 0)

watchEffect(() => console.log(isEven.value)) // true

// sẽ không log thêm vì giá trị computed vẫn là `true`
count.value = 2
count.value = 4
```

Điều này giúp giảm các lần kích hoạt effect không cần thiết. Tuy vậy, nó sẽ không hiệu quả nếu computed tạo ra một object mới ở mỗi lần tính:

```js
const computedObj = computed(() => {
  return {
    isEven: count.value % 2 === 0
  }
})
```

Vì một object mới được tạo ra mỗi lần, nên về mặt kỹ thuật giá trị mới luôn khác giá trị cũ. Ngay cả khi property `isEven` vẫn không đổi, Vue cũng không thể biết điều đó nếu không thực hiện so sánh sâu giữa giá trị cũ và giá trị mới. Việc so sánh như vậy có thể tốn kém và thường không đáng.

Thay vào đó, ta có thể tối ưu bằng cách tự so sánh giá trị mới với giá trị cũ, rồi chỉ trả lại giá trị cũ nếu biết chắc là không có gì đổi:

```js
const computedObj = computed((oldValue) => {
  const newValue = {
    isEven: count.value % 2 === 0
  }
  if (oldValue && oldValue.isEven === newValue.isEven) {
    return oldValue
  }
  return newValue
})
```

[Thử trên Playground](https://play.vuejs.org/#eNqVVMtu2zAQ/JUFgSZK4UpuczMkow/40AJ9IC3aQ9mDIlG2EokUyKVt1PC/d0lKtoEminMQQC1nZ4c7S+7Yu66L11awGUtNoesOwQi03ZzLuu2URtiBFtUECtV2FkU5gU2OxWpRVaJA2EOlVQuXxHDJJZeFkgYJayVC5hKj6dUxLnzSjZXmV40rZfFrh3Vb/82xVrLH//5DCQNNKPkweNiNVFP+zBsrIJvDjksgGrRahjVAbRZrIWdBVLz2yBfwBrIsg6mD7LncPyryfIVnywupUmz68HOEEqqCI+XFBQzrOKR79MDdx66GCn1jhpQDZx8f0oZ+nBgdRVcH/aMuBt1xZ80qGvGvh/X6nlXwnGpPl6qsLLxTtitzFFTNl0oSN/79AKOCHHQuS5pw4XorbXsr9ImHZN7nHFdx1SilI78MeOJ7Ca+nbvgd+GgomQOv6CNjSQqXaRJuHd03+kHRdg3JoT+A3a7XsfcmpbcWkQS/LZq6uM84C8o5m4fFuOg0CemeOXXX2w2E6ylsgj2gTgeYio/f1l5UEqj+Z3yC7lGuNDlpApswNNTrql7Gd0ZJeqW8TZw5t+tGaMdDXnA2G4acs7xp1OaTj6G2YjLEi5Uo7h+I35mti3H2TQsj9Jp6etjDXC8Fhu3F9y9iS+vDZqtK2xB6ZPNGGNVYpzHA3ltZkuwTnFf70b+1tVz+MIstCmmGQzmh/p56PGf00H4YOfpR7nV8PTxubP8P2GAP9Q==)

Lưu ý là bạn luôn nên thực hiện đầy đủ phép tính trước khi so sánh và trả lại giá trị cũ, để cùng một tập dependency vẫn được thu thập ở mỗi lần chạy.

## Tối ưu tổng quát {#general-optimizations}

> Các mẹo dưới đây ảnh hưởng tới cả hiệu năng tải trang lẫn hiệu năng cập nhật.

### Virtualize danh sách lớn {#virtualize-large-lists}

Một trong những vấn đề hiệu năng phổ biến nhất trong mọi ứng dụng frontend là render danh sách lớn. Dù framework có nhanh đến đâu, việc render một danh sách với hàng nghìn item **vẫn** chậm vì trình duyệt phải xử lý quá nhiều DOM node.

Tuy vậy, ta không nhất thiết phải render toàn bộ số node đó ngay từ đầu. Trong phần lớn trường hợp, kích thước màn hình của người dùng chỉ hiển thị được một phần nhỏ trong danh sách lớn. Ta có thể cải thiện hiệu năng rất nhiều bằng **list virtualization**, tức là chỉ render những item hiện đang nằm trong hoặc gần viewport.

Việc tự cài list virtualization không hề đơn giản. May là đã có các thư viện cộng đồng mà bạn có thể dùng ngay:

- [vue-virtual-scroller](https://github.com/Akryum/vue-virtual-scroller)
- [vue-virtual-scroll-grid](https://github.com/rocwang/vue-virtual-scroll-grid)
- [vueuc/VVirtualList](https://github.com/07akioni/vueuc)

### Giảm overhead của tính phản ứng với cấu trúc lớn nhưng bất biến {#reduce-reactivity-overhead-for-large-immutable-structures}

Hệ thống tính phản ứng của Vue mặc định là sâu. Điều này giúp việc quản lý state tự nhiên hơn, nhưng cũng tạo ra một mức overhead nhất định khi dữ liệu quá lớn, vì mỗi lần truy cập property đều đi qua proxy trap để theo dõi dependency. Điều này thường thấy rõ khi làm việc với những mảng lớn chứa các object lồng nhau sâu, nơi chỉ một lần render cũng có thể đọc hơn 100.000 property. Tuy nhiên, nó chỉ ảnh hưởng đến những trường hợp khá đặc thù.

Vue có cung cấp một lối thoát để bỏ qua tính phản ứng sâu, bằng cách dùng [`shallowRef()`](/api/reactivity-advanced#shallowref) và [`shallowReactive()`](/api/reactivity-advanced#shallowreactive). Các API shallow tạo ra state chỉ phản ứng ở cấp gốc, còn mọi object lồng bên trong sẽ được giữ nguyên. Nhờ đó, việc truy cập property lồng bên trong sẽ nhanh hơn, đổi lại là giờ đây ta phải xem toàn bộ object lồng bên trong là bất biến, và chỉ có thể kích hoạt cập nhật bằng cách thay thế state ở gốc:

```js
const shallowArray = shallowRef([
  /* danh sách lớn gồm các object lồng nhau sâu */
])

// cách này sẽ không kích hoạt cập nhật...
shallowArray.value.push(newObject)
// còn cách này thì có:
shallowArray.value = [...shallowArray.value, newObject]

// cách này cũng sẽ không kích hoạt cập nhật...
shallowArray.value[0].foo = 1
// còn cách này thì có:
shallowArray.value = [
  {
    ...shallowArray.value[0],
    foo: 1
  },
  ...shallowArray.value.slice(1)
]
```

### Tránh trừu tượng hóa component không cần thiết {#avoid-unnecessary-component-abstractions}

Đôi khi ta tạo ra [renderless component](/guide/components/slots#renderless-components) hoặc higher-order component, tức component render ra component khác cùng với prop bổ sung, để có mức trừu tượng hoặc tổ chức code tốt hơn. Điều này không sai, nhưng cần nhớ rằng instance của component tốn chi phí hơn nhiều so với DOM node thông thường. Nếu tạo quá nhiều component chỉ vì mô hình trừu tượng hóa, bạn sẽ phải trả giá về hiệu năng.

Lưu ý là nếu chỉ giảm đi một vài instance thì thường sẽ không có khác biệt đáng kể, nên không cần quá lo nếu component đó chỉ render vài lần trong ứng dụng. Tình huống đáng để cân nhắc tối ưu này vẫn là trong các danh sách lớn. Hãy tưởng tượng một danh sách 100 item mà mỗi item component lại chứa rất nhiều component con. Chỉ cần bỏ đi một lớp trừu tượng component không cần thiết ở đây cũng có thể giảm đi hàng trăm component instance.
