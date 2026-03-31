# Hook Vòng Đời {#lifecycle-hooks}

Mỗi instance của component Vue trải qua một chuỗi các bước khởi tạo khi được tạo ra — ví dụ: thiết lập quan sát dữ liệu, biên dịch template, mount instance vào DOM, và cập nhật DOM khi dữ liệu thay đổi. Trong quá trình đó, Vue cũng gọi các hàm được gọi là hook vòng đời, giúp người dùng có cơ hội thêm code của mình vào từng giai đoạn cụ thể.

## Đăng Ký Hook Vòng Đời {#registering-lifecycle-hooks}

Ví dụ, hook <span class="composition-api">`onMounted`</span><span class="options-api">`mounted`</span> có thể được dùng để chạy code sau khi component đã hoàn tất lần render đầu tiên và các DOM node đã được tạo ra:

<div class="composition-api">

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  mounted() {
    console.log(`the component is now mounted.`)
  }
}
```

</div>

Ngoài ra còn có các hook khác được gọi ở những giai đoạn khác nhau trong vòng đời của instance, trong đó được dùng phổ biến nhất là <span class="composition-api">[`onMounted`](/api/composition-api-lifecycle#onmounted), [`onUpdated`](/api/composition-api-lifecycle#onupdated) và [`onUnmounted`](/api/composition-api-lifecycle#onunmounted).</span><span class="options-api">[`mounted`](/api/options-lifecycle#mounted), [`updated`](/api/options-lifecycle#updated) và [`unmounted`](/api/options-lifecycle#unmounted).</span>

<div class="options-api">

Tất cả hook vòng đời đều được gọi với ngữ cảnh `this` trỏ đến instance đang hoạt động hiện tại. Điều này có nghĩa là bạn nên tránh dùng arrow function khi khai báo hook vòng đời, vì nếu làm vậy bạn sẽ không thể truy cập instance của component qua `this`.

</div>

<div class="composition-api">

Khi gọi `onMounted`, Vue tự động liên kết hàm callback được đăng ký với instance component đang hoạt động hiện tại. Điều này yêu cầu các hook phải được đăng ký **đồng bộ** trong quá trình setup của component. Ví dụ, không nên làm như sau:

```js
setTimeout(() => {
  onMounted(() => {
    // this won't work.
  })
}, 100)
```

Lưu ý rằng điều này không có nghĩa là lời gọi phải được đặt theo đúng thứ tự từ điển bên trong `setup()` hay `<script setup>`. `onMounted()` có thể được gọi trong một hàm bên ngoài, miễn là call stack là đồng bộ và có nguồn gốc từ bên trong `setup()`.

</div>

## Sơ Đồ Vòng Đời {#lifecycle-diagram}

Dưới đây là sơ đồ vòng đời của một instance. Bạn chưa cần hiểu hết mọi thứ ngay lúc này, nhưng khi học và xây dựng nhiều hơn, đây sẽ là tài liệu tham khảo hữu ích.

![Component lifecycle diagram](./images/lifecycle.png)

<!-- https://www.figma.com/file/Xw3UeNMOralY6NV7gSjWdS/Vue-Lifecycle -->

Tham khảo <span class="composition-api">[tài liệu API hook vòng đời](/api/composition-api-lifecycle)</span><span class="options-api">[tài liệu API hook vòng đời](/api/options-lifecycle)</span> để biết thêm chi tiết về tất cả hook vòng đời và các trường hợp sử dụng tương ứng.
