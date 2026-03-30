# Bắt đầu {#getting-started}

Chào mừng bạn đến với hướng dẫn Vue!

Mục tiêu của hướng dẫn này là nhanh chóng cho bạn trải nghiệm cảm giác làm việc với Vue ngay trong trình duyệt. Nó không nhằm mục đích toàn diện, và bạn không cần hiểu mọi thứ trước khi tiếp tục. Tuy nhiên, sau khi hoàn thành, hãy chắc chắn đọc <a target="_blank" href="/guide/introduction.html">Hướng dẫn</a> bao gồm từng chủ đề chi tiết hơn.

## Điều kiện tiên quyết {#prerequisites}

Hướng dẫn này giả định bạn đã quen cơ bản với HTML, CSS và JavaScript. Nếu bạn hoàn toàn mới với phát triển front-end, có thể không phải là ý tưởng tốt khi nhảy thẳng vào một framework làm bước đầu tiên - hãy nắm vững các kiến thức cơ bản rồi quay lại! Kinh nghiệm với các framework khác sẽ giúp ích, nhưng không bắt buộc.

## Cách Sử dụng Hướng dẫn Này {#how-to-use-this-tutorial}

Bạn có thể chỉnh sửa code <span class="wide">ở bên phải</span><span class="narrow">bên dưới</span> và thấy kết quả cập nhật tức thì. Mỗi bước sẽ giới thiệu một tính năng cốt lõi của Vue, và bạn sẽ cần hoàn thiện code để demo hoạt động. Nếu bị mắc kẹt, bạn sẽ có nút "Chỉ cho tôi!" hiển thị code hoạt động cho bạn. Cố gắng đừng phụ thuộc vào nó quá nhiều - bạn sẽ học nhanh hơn khi tự tìm ra.

Nếu bạn là developer có kinh nghiệm đến từ Vue 2 hoặc các framework khác, có một số cài đặt bạn có thể điều chỉnh để tận dụng tốt nhất hướng dẫn này. Nếu bạn là người mới bắt đầu, khuyến nghị dùng các cài đặt mặc định.

<details>
<summary>Chi tiết Cài đặt Hướng dẫn</summary>

- Vue cung cấp hai phong cách API: Options API và Composition API. Hướng dẫn này được thiết kế để hoạt động với cả hai - bạn có thể chọn phong cách ưa thích bằng nút **API Preference** ở trên cùng. <a target="_blank" href="/guide/introduction.html#api-styles">Tìm hiểu thêm về phong cách API</a>.

- Bạn cũng có thể chuyển đổi giữa chế độ SFC hoặc HTML. Chế độ trước sẽ hiển thị ví dụ code theo định dạng <a target="_blank" href="/guide/introduction.html#single-file-components">Single-File Component</a> (SFC), đây là cách hầu hết developer dùng khi sử dụng Vue với build step. Chế độ HTML hiển thị cách dùng không có build step.

<div class="html">

:::tip
Nếu bạn sắp dùng chế độ HTML không có build step trong ứng dụng của mình, hãy đảm bảo bạn thay đổi các import thành:

```js
import { ... } from 'vue/dist/vue.esm-bundler.js'
```

bên trong script của bạn hoặc cấu hình build tool để resolve `vue` tương ứng. Cấu hình mẫu cho [Vite](https://vite.dev/):

```js [vite.config.js]
export default {
  resolve: {
    alias: {
      vue: 'vue/dist/vue.esm-bundler.js'
    }
  }
}
```

Xem [phần tương ứng trong hướng dẫn Tooling](/guide/scaling-up/tooling.html#note-on-in-browser-template-compilation) để biết thêm thông tin.
:::

</div>

</details>

Sẵn sàng chưa? Nhấn "Tiếp theo" để bắt đầu.
