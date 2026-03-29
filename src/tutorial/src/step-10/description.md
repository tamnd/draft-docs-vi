# Watcher {#watchers}

Đôi khi chúng ta có thể cần thực hiện "side effect" một cách reactive - ví dụ: in một số vào console khi nó thay đổi. Chúng ta có thể đạt điều này với watcher:

<div class="composition-api">

```js
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newCount) => {
  // đúng, console.log() là một side effect
  console.log(`new count is: ${newCount}`)
})
```

`watch()` có thể trực tiếp theo dõi một ref, và callback được kích hoạt bất cứ khi nào giá trị của `count` thay đổi. `watch()` cũng có thể theo dõi các loại nguồn dữ liệu khác - chi tiết thêm được đề cập trong <a target="_blank" href="/guide/essentials/watchers.html">Hướng dẫn - Watcher</a>.

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  watch: {
    count(newCount) {
      // đúng, console.log() là một side effect
      console.log(`new count is: ${newCount}`)
    }
  }
}
```

Ở đây, chúng ta dùng tùy chọn `watch` để theo dõi các thay đổi của thuộc tính `count`. Callback watch được gọi khi `count` thay đổi, và nhận giá trị mới làm đối số. Chi tiết thêm được đề cập trong <a target="_blank" href="/guide/essentials/watchers.html">Hướng dẫn - Watcher</a>.

</div>

Ví dụ thực tế hơn là log vào console là lấy dữ liệu mới khi ID thay đổi. Code chúng ta đang có lấy dữ liệu todos từ mock API khi component mount. Cũng có nút tăng todo ID cần được lấy. Hãy thử triển khai watcher để lấy todo mới khi nút được nhấn.
