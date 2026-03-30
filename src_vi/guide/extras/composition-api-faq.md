---
outline: deep
---

# Câu Hỏi Thường Gặp Về Composition API {#composition-api-faq}

:::tip
Phần FAQ này giả định rằng bạn đã có kinh nghiệm với Vue, đặc biệt là Vue 2 và chủ yếu dùng Options API.
:::

## Composition API Là Gì? {#what-is-composition-api}

<VueSchoolLink href="https://vueschool.io/lessons/introduction-to-the-vue-js-3-composition-api" title="Bài học miễn phí về Composition API"/>

Composition API là một tập hợp API cho phép ta viết component Vue bằng các hàm được import, thay vì khai báo bằng options. Đây là một thuật ngữ bao trùm các API sau:

- [Reactivity API](/api/reactivity-core), ví dụ `ref()` và `reactive()`, cho phép ta trực tiếp tạo state phản ứng, state computed và watcher.

- [Lifecycle Hooks](/api/composition-api-lifecycle), ví dụ `onMounted()` và `onUnmounted()`, cho phép ta can thiệp vào vòng đời của component bằng mã lệnh.

- [Dependency Injection](/api/composition-api-dependency-injection), tức `provide()` và `inject()`, cho phép ta tận dụng hệ thống tiêm phụ thuộc của Vue khi dùng Reactivity API.

Composition API là tính năng dựng sẵn của Vue 3 và [Vue 2.7](https://blog.vuejs.org/posts/vue-2-7-naruto.html). Với các phiên bản Vue 2 cũ hơn, hãy dùng plugin [`@vue/composition-api`](https://github.com/vuejs/composition-api) do chính nhóm Vue duy trì. Trong Vue 3, nó cũng thường được dùng cùng cú pháp [`<script setup>`](/api/sfc-script-setup) trong Single-File Component. Đây là một ví dụ cơ bản về component dùng Composition API:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// state phản ứng
const count = ref(0)

// hàm thay đổi state và kích hoạt cập nhật
function increment() {
  count.value++
}

// hook vòng đời
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

Dù có phong cách API dựa trên việc kết hợp các hàm, **Composition API KHÔNG PHẢI là lập trình hàm**. Composition API dựa trên mô hình phản ứng chi tiết và có thể thay đổi của Vue, trong khi lập trình hàm nhấn mạnh tính bất biến.

Nếu bạn muốn học cách dùng Vue với Composition API, bạn có thể đổi API preference của toàn bộ trang sang Composition API bằng nút chuyển ở đầu thanh bên trái, sau đó đi qua phần hướng dẫn từ đầu.

## Vì Sao Có Composition API? {#why-composition-api}

### Tái Sử Dụng Logic Tốt Hơn {#better-logic-reuse}

Ưu điểm chính của Composition API là nó cho phép tái sử dụng logic một cách gọn gàng và hiệu quả dưới dạng [hàm composable](/guide/reusability/composables). Nó giải quyết [mọi nhược điểm của mixin](/guide/reusability/composables#vs-mixins), vốn là cơ chế tái sử dụng logic chính của Options API.

Khả năng tái sử dụng logic của Composition API đã tạo điều kiện cho nhiều dự án cộng đồng ấn tượng như [VueUse](https://vueuse.org/), một bộ sưu tập utility composable ngày càng lớn. Nó cũng là một cơ chế sạch sẽ để tích hợp các dịch vụ hoặc thư viện bên thứ ba có state vào hệ thống tính phản ứng của Vue, ví dụ [dữ liệu bất biến](/guide/extras/reactivity-in-depth#immutable-data), [state machine](/guide/extras/reactivity-in-depth#state-machines) và [RxJS](/guide/extras/reactivity-in-depth#rxjs).

### Tổ Chức Mã Linh Hoạt Hơn {#more-flexible-code-organization}

Nhiều người dùng thích việc Options API mặc định buộc mã được tổ chức theo khuôn mẫu: mọi thứ đều có chỗ của nó tùy theo option tương ứng. Tuy nhiên, Options API bộc lộ giới hạn rõ rệt khi logic của một component vượt qua một ngưỡng phức tạp nhất định. Điều này đặc biệt dễ thấy ở những component phải xử lý nhiều **mối quan tâm logic** khác nhau, và đây là điều chúng tôi đã chứng kiến trực tiếp trong rất nhiều ứng dụng Vue 2 chạy production.

Lấy ví dụ component duyệt thư mục trong giao diện của Vue CLI: component này chịu trách nhiệm cho các mối quan tâm logic sau:

- Theo dõi state của thư mục hiện tại và hiển thị nội dung của nó
- Xử lý việc điều hướng thư mục (mở, đóng, làm mới...)
- Xử lý việc tạo thư mục mới
- Bật / tắt chế độ chỉ hiển thị thư mục yêu thích
- Bật / tắt chế độ hiển thị thư mục ẩn
- Xử lý thay đổi thư mục làm việc hiện tại

[Phiên bản gốc](https://github.com/vuejs/vue-cli/blob/a09407dd5b9f18ace7501ddb603b95e31d6d93c0/packages/@vue/cli-ui/src/components/folder/FolderExplorer.vue#L198-L404) của component này được viết bằng Options API. Nếu tô màu từng dòng mã theo mối quan tâm logic mà nó xử lý, ta sẽ có kết quả như sau:

<img alt="folder component before" src="./images/options-api.png" width="129" height="500" style="margin: 1.2em auto">

Hãy chú ý cách mà đoạn mã xử lý cùng một mối quan tâm logic bị buộc phải phân tán vào nhiều option khác nhau, nằm ở nhiều vị trí khác nhau trong file. Với một component dài hàng trăm dòng, việc hiểu và lần theo chỉ một mối quan tâm logic sẽ đòi hỏi phải cuộn lên xuống liên tục, khiến công việc khó hơn rất nhiều so với mức cần thiết. Thêm vào đó, nếu ta muốn tách một mối quan tâm logic ra thành utility có thể tái sử dụng, việc tìm và gom đúng các đoạn mã ở những nơi khác nhau cũng tốn khá nhiều công sức.

Đây là cùng component đó, trước và sau khi [refactor sang Composition API](https://gist.github.com/yyx990803/8854f8f6a97631576c14b63c8acd8f2e):

![folder component after](./images/composition-api-after.png)

Bạn có thể thấy mã liên quan đến cùng một mối quan tâm logic giờ đã có thể được nhóm lại với nhau: ta không còn phải nhảy qua lại giữa các khối option khác nhau khi làm việc trên một logic cụ thể. Hơn nữa, giờ đây ta có thể di chuyển cả một nhóm mã sang file bên ngoài với rất ít công sức, vì không còn phải xáo trộn lại mã chỉ để tách nó ra. Việc giảm ma sát khi refactor như vậy là yếu tố rất quan trọng đối với khả năng bảo trì dài hạn của các codebase lớn.

### Suy Luận Kiểu Tốt Hơn {#better-type-inference}

Trong những năm gần đây, ngày càng nhiều frontend developer chuyển sang dùng [TypeScript](https://www.typescriptlang.org/) vì nó giúp ta viết mã chắc chắn hơn, thay đổi tự tin hơn và có trải nghiệm phát triển tốt hơn nhờ IDE hỗ trợ. Tuy nhiên, Options API, vốn được hình thành từ năm 2013, ban đầu không được thiết kế với mục tiêu suy luận kiểu. Chúng tôi đã phải triển khai một số [kỹ thuật kiểu cực kỳ phức tạp](https://github.com/vuejs/core/blob/44b95276f5c086e1d88fa3c686a5f39eb5bb7821/packages/runtime-core/src/componentPublicInstance.ts#L132-L165) để suy luận kiểu có thể hoạt động với Options API. Ngay cả với tất cả nỗ lực đó, suy luận kiểu của Options API vẫn có thể gãy khi dùng mixin và dependency injection.

Điều này khiến nhiều developer muốn dùng Vue với TS nghiêng sang Class API được hỗ trợ bởi `vue-class-component`. Tuy nhiên, API dựa trên class phụ thuộc rất nặng vào ES decorators, một tính năng ngôn ngữ mới chỉ ở mức proposal stage 2 khi Vue 3 đang được phát triển vào năm 2019. Chúng tôi cho rằng việc xây một API chính thức trên một proposal chưa ổn định là quá rủi ro. Kể từ đó, proposal decorators lại tiếp tục bị thay đổi toàn diện một lần nữa, và đến năm 2022 mới đạt stage 3. Ngoài ra, API dựa trên class cũng gặp các hạn chế về tái sử dụng logic và tổ chức mã tương tự Options API.

Trong khi đó, Composition API chủ yếu tận dụng các biến và hàm thuần JavaScript, vốn tự nhiên thân thiện với hệ kiểu. Mã viết bằng Composition API có thể tận dụng suy luận kiểu đầy đủ mà gần như không cần thêm chú thích kiểu thủ công. Phần lớn thời gian, mã Composition API trông gần như giống hệt nhau giữa TypeScript và JavaScript thuần. Điều này cũng giúp người dùng JavaScript thuần được hưởng lợi từ suy luận kiểu ở một mức nhất định.

### Bundle Production Nhỏ Hơn Và Ít Overhead Hơn {#smaller-production-bundle-and-less-overhead}

Mã viết bằng Composition API và `<script setup>` cũng hiệu quả hơn và thân thiện với việc minify hơn so với mã Options API tương đương. Lý do là vì template trong một component `<script setup>` được biên dịch thành một hàm nội tuyến nằm trong cùng scope với mã `<script setup>`. Khác với truy cập property qua `this`, mã template đã biên dịch có thể truy cập trực tiếp các biến khai báo bên trong `<script setup>` mà không cần đi qua instance proxy. Điều này cũng giúp minify tốt hơn, vì toàn bộ tên biến đều có thể được rút gọn một cách an toàn.

## Mối Quan Hệ Với Options API {#relationship-with-options-api}

### Đánh Đổi {#trade-offs}

Một số người dùng chuyển từ Options API sang thấy mã Composition API của họ ít có tổ chức hơn, và kết luận rằng Composition API "tệ hơn" về mặt tổ chức mã. Với những quan điểm như vậy, chúng tôi khuyến nghị nên nhìn vấn đề từ một góc độ khác.

Đúng là Composition API không còn đưa ra các "thanh chắn" để hướng bạn đặt mã vào các ngăn tương ứng nữa. Bù lại, bạn có thể viết mã component giống như cách viết JavaScript thông thường. Điều này có nghĩa là **bạn có thể và nên áp dụng mọi best practice về tổ chức mã cho Composition API giống như khi viết JavaScript bình thường**. Nếu bạn có thể viết JavaScript có tổ chức tốt, bạn cũng có thể viết Composition API có tổ chức tốt.

Options API đúng là cho phép bạn "nghĩ ít hơn" khi viết mã component, và đó là lý do nhiều người dùng yêu thích nó. Tuy nhiên, việc giảm gánh nặng suy nghĩ này cũng đồng thời khóa bạn vào một mô hình tổ chức mã được định sẵn mà không có lối thoát, khiến việc refactor hoặc cải thiện chất lượng mã trong những dự án quy mô lớn trở nên khó khăn. Ở khía cạnh này, Composition API mang lại khả năng mở rộng về lâu dài tốt hơn.

### Composition API Có Bao Phủ Mọi Trường Hợp Sử Dụng Không? {#does-composition-api-cover-all-use-cases}

Có, nếu xét ở khía cạnh logic có state. Khi dùng Composition API, chỉ còn một vài option đôi khi vẫn cần đến: `props`, `emits`, `name` và `inheritAttrs`.

:::tip

Từ 3.3, bạn có thể dùng trực tiếp `defineOptions` trong `<script setup>` để đặt tên component hoặc property `inheritAttrs`

:::

Nếu bạn dự định chỉ dùng Composition API (cùng với các option liệt kê ở trên), bạn có thể giảm thêm vài kb trong bundle production bằng [compile-time flag](/api/compile-time-flags) để loại bỏ phần mã liên quan đến Options API khỏi Vue. Lưu ý rằng điều này cũng ảnh hưởng đến các component Vue đến từ dependency của bạn.

### Tôi Có Thể Dùng Cả Hai API Trong Cùng Một Component Không? {#can-i-use-both-apis-in-the-same-component}

Có. Bạn có thể dùng Composition API thông qua option [`setup()`](/api/composition-api-setup) trong một component Options API.

Tuy nhiên, chúng tôi chỉ khuyến nghị làm như vậy nếu bạn đang có sẵn một codebase Options API và cần tích hợp các tính năng mới hoặc thư viện bên ngoài được viết bằng Composition API.

### Options API Có Bị Khai Tử Không? {#will-options-api-be-deprecated}

Không, chúng tôi không có kế hoạch làm vậy. Options API là một phần không thể tách rời của Vue và cũng là lý do nhiều developer yêu thích Vue. Chúng tôi cũng nhận thấy rằng nhiều lợi ích của Composition API chỉ thật sự phát huy trong các dự án quy mô lớn, còn Options API vẫn là lựa chọn chắc chắn cho nhiều trường hợp có độ phức tạp thấp đến trung bình.

## Mối Quan Hệ Với Class API {#relationship-with-class-api}

Chúng tôi không còn khuyến nghị dùng Class API với Vue 3 nữa, vì Composition API đã cung cấp khả năng tích hợp TypeScript rất tốt cùng với lợi ích bổ sung về tái sử dụng logic và tổ chức mã.

## So Sánh Với React Hooks {#comparison-with-react-hooks}

Composition API mang lại mức độ kết hợp logic tương đương React Hooks, nhưng có một số khác biệt quan trọng.

React Hooks được gọi lặp lại mỗi khi component cập nhật. Điều này tạo ra một số điểm cần lưu ý mà ngay cả developer React nhiều kinh nghiệm cũng có thể thấy khó chịu. Nó cũng kéo theo các vấn đề tối ưu hiệu năng có thể ảnh hưởng mạnh đến trải nghiệm phát triển. Ví dụ:

- Hooks nhạy cảm với thứ tự gọi và không thể dùng có điều kiện.

- Các biến khai báo trong component React có thể bị một closure của hook "bắt" lại và trở nên lỗi thời nếu developer không truyền đúng dependencies array. Điều này khiến developer React phải dựa vào rule ESLint để chắc chắn dependencies được truyền đúng. Tuy nhiên, rule đó thường không đủ thông minh và dễ bù quá tay cho tính đúng đắn, dẫn đến invalidate không cần thiết và gây đau đầu khi gặp edge case.

- Những phép tính tốn kém đòi hỏi phải dùng `useMemo`, và một lần nữa lại cần truyền đúng dependencies array bằng tay.

- Event handler truyền xuống component con mặc định sẽ gây cập nhật component con không cần thiết, và muốn tối ưu phải dùng `useCallback` một cách tường minh. Điều này gần như luôn cần thiết, lại tiếp tục đòi hỏi một dependencies array chính xác. Nếu bỏ qua, ứng dụng sẽ dễ bị render thừa mặc định và có thể nảy sinh vấn đề hiệu năng mà không nhận ra.

- Vấn đề stale closure, kết hợp với các tính năng Concurrent, khiến việc suy luận xem một đoạn mã hooks chạy vào lúc nào trở nên khó khăn, đồng thời làm cho việc thao tác với mutable state cần được giữ qua các lần render (thông qua `useRef`) trở nên cồng kềnh.

> Lưu ý: một số vấn đề ở trên liên quan đến memoization có thể được giải quyết bởi [React Compiler](https://react.dev/learn/react-compiler) sắp ra mắt.

Trong khi đó, Vue Composition API:

- Chỉ gọi mã trong `setup()` hoặc `<script setup>` đúng một lần. Điều này khiến mã khớp hơn với trực giác khi dùng JavaScript thông thường vì không có stale closure để phải bận tâm. Các lời gọi Composition API cũng không nhạy cảm với thứ tự gọi và có thể dùng có điều kiện.

- Hệ thống tính phản ứng runtime của Vue tự động thu thập các dependency phản ứng được dùng trong computed và watcher, nên không cần phải khai báo dependency thủ công.

- Không cần cache callback thủ công để tránh cập nhật component con không cần thiết. Nhìn chung, hệ thống tính phản ứng chi tiết của Vue bảo đảm component con chỉ cập nhật khi thực sự cần. Việc tối ưu cập nhật component con bằng tay hiếm khi là mối bận tâm với developer Vue.

Chúng tôi ghi nhận sự sáng tạo của React Hooks, và đó là một nguồn cảm hứng lớn cho Composition API. Tuy nhiên, những vấn đề nêu trên thực sự tồn tại trong thiết kế của nó, và chúng tôi nhận thấy mô hình tính phản ứng của Vue vô tình cung cấp một lối đi tránh được các vấn đề đó.
