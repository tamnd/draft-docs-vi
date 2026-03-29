# Hook vòng đời {#lifecycle-hooks}

Mỗi component instance trong Vue sẽ đi qua một loạt bước khởi tạo khi được tạo ra. Ví dụ, nó cần thiết lập việc theo dõi dữ liệu, biên dịch template, mount instance vào DOM và cập nhật DOM khi dữ liệu thay đổi. Trong suốt quá trình đó, nó cũng chạy những hàm gọi là hook vòng đời, cho phép người dùng chèn thêm code của mình vào những giai đoạn cụ thể.

## Đăng ký hook vòng đời {#registering-lifecycle-hooks}

Ví dụ, hook <span class="composition-api">`onMounted`</span><span class="options-api">`mounted`</span> có thể được dùng để chạy code sau khi component hoàn tất lần render đầu tiên và đã tạo ra các DOM node:

<div class="composition-api">

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`component đã được mount.`)
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  mounted() {
    console.log(`component đã được mount.`)
  }
}
```

</div>

Ngoài ra còn có những hook khác sẽ được gọi ở các giai đoạn khác nhau trong vòng đời của instance. Những hook được dùng phổ biến nhất là <span class="composition-api">[`onMounted`](/api/composition-api-lifecycle#onmounted), [`onUpdated`](/api/composition-api-lifecycle#onupdated) và [`onUnmounted`](/api/composition-api-lifecycle#onunmounted).</span><span class="options-api">[`mounted`](/api/options-lifecycle#mounted), [`updated`](/api/options-lifecycle#updated) và [`unmounted`](/api/options-lifecycle#unmounted).</span>

<div class="options-api">

Mọi hook vòng đời đều được gọi với ngữ cảnh `this` trỏ tới instance hiện tại đang gọi nó. Điều này có nghĩa là bạn nên tránh dùng arrow function khi khai báo hook vòng đời, vì nếu làm vậy bạn sẽ không thể truy cập component instance thông qua `this`.

</div>

<div class="composition-api">

Khi gọi `onMounted`, Vue sẽ tự động gắn callback đã đăng ký với component instance hiện tại đang hoạt động. Vì vậy, các hook này phải được đăng ký một cách **đồng bộ** trong lúc thiết lập component. Ví dụ, đừng làm như sau:

```js
setTimeout(() => {
  onMounted(() => {
    // cách này sẽ không hoạt động.
  })
}, 100)
```

Cũng cần lưu ý rằng điều này không có nghĩa lời gọi đó bắt buộc phải nằm trực tiếp bên trong `setup()` hoặc `<script setup>`. `onMounted()` vẫn có thể được gọi trong một hàm bên ngoài, miễn là call stack vẫn là đồng bộ và bắt nguồn từ bên trong `setup()`.

</div>

## Sơ đồ vòng đời {#lifecycle-diagram}

Dưới đây là sơ đồ vòng đời của instance. Bạn chưa cần phải hiểu hết mọi thứ ngay lúc này, nhưng khi học sâu hơn và xây dựng nhiều hơn, nó sẽ là một tài liệu tham khảo hữu ích.

![Sơ đồ vòng đời của component](./images/lifecycle.png)

<!-- https://www.figma.com/file/Xw3UeNMOralY6NV7gSjWdS/Vue-Lifecycle -->

Hãy xem <span class="composition-api">[tài liệu API về hook vòng đời](/api/composition-api-lifecycle)</span><span class="options-api">[tài liệu API về hook vòng đời](/api/options-lifecycle)</span> để biết chi tiết về toàn bộ hook vòng đời và trường hợp sử dụng của chúng.
