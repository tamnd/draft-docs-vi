# Vòng đời và Template Ref {#lifecycle-and-template-refs}

Cho đến nay, Vue đã xử lý tất cả cập nhật DOM cho chúng ta, nhờ tính phản ứng và kết xuất khai báo. Tuy nhiên, chắc chắn sẽ có những trường hợp chúng ta cần làm việc trực tiếp với DOM.

Chúng ta có thể yêu cầu **template ref** - tức là tham chiếu đến một element trong template - bằng cách dùng <a target="_blank" href="/api/built-in-special-attributes.html#ref">thuộc tính đặc biệt `ref`</a>:

```vue-html
<p ref="pElementRef">hello</p>
```

<div class="composition-api">

Để truy cập ref, chúng ta cần khai báo<span class="html"> và expose</span> một ref với tên khớp:

<div class="sfc">

```js
const pElementRef = ref(null)
```

</div>
<div class="html">

```js
setup() {
  const pElementRef = ref(null)

  return {
    pElementRef
  }
}
```

</div>

Lưu ý ref được khởi tạo với giá trị `null`. Điều này vì element chưa tồn tại khi <span class="sfc">`<script setup>`</span><span class="html">`setup()`</span> được thực thi. Template ref chỉ có thể truy cập sau khi component được **mount**.

Để chạy code sau khi mount, chúng ta có thể dùng hàm `onMounted()`:

<div class="sfc">

```js
import { onMounted } from 'vue'

onMounted(() => {
  // component đã được mount.
})
```

</div>
<div class="html">

```js
import { onMounted } from 'vue'

createApp({
  setup() {
    onMounted(() => {
      // component đã được mount.
    })
  }
})
```

</div>
</div>

<div class="options-api">

Element sẽ được expose trên `this.$refs` là `this.$refs.pElementRef`. Tuy nhiên, bạn chỉ có thể truy cập nó sau khi component được **mount**.

Để chạy code sau khi mount, chúng ta có thể dùng tùy chọn `mounted`:

<div class="sfc">

```js
export default {
  mounted() {
    // component đã được mount.
  }
}
```

</div>
<div class="html">

```js
createApp({
  mounted() {
    // component đã được mount.
  }
})
```

</div>
</div>

Đây được gọi là **lifecycle hook** - nó cho phép chúng ta đăng ký callback được gọi vào các thời điểm nhất định trong vòng đời của component. Có các hook khác như <span class="options-api">`created` và `updated`</span><span class="composition-api">`onUpdated` và `onUnmounted`</span>. Xem <a target="_blank" href="/guide/essentials/lifecycle.html#lifecycle-diagram">Sơ đồ Vòng đời</a> để biết thêm chi tiết.

Bây giờ, hãy thử thêm hook <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span>, truy cập `<p>` qua <span class="options-api">`this.$refs.pElementRef`</span><span class="composition-api">`pElementRef.value`</span>, và thực hiện một số thao tác DOM trực tiếp trên nó (ví dụ: thay đổi `textContent` của nó).
