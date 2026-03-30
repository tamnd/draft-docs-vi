# Plugin {#plugins}

## Giới Thiệu {#introduction}

Plugin là những đoạn mã khép kín, thường dùng để bổ sung chức năng ở cấp ứng dụng cho Vue. Đây là cách cài đặt một plugin:

```js
import { createApp } from 'vue'

const app = createApp({})

app.use(myPlugin, {
  /* các option tùy chọn */
})
```

Plugin được định nghĩa dưới dạng một object có phương thức `install()`, hoặc đơn giản là một hàm đóng vai trò hàm cài đặt. Hàm cài đặt nhận [instance app](/api/application) cùng với các option bổ sung được truyền vào `app.use()`, nếu có:

```js
const myPlugin = {
  install(app, options) {
    // cấu hình app
  }
}
```

Không có phạm vi nào được định nghĩa cứng cho plugin, nhưng những trường hợp phổ biến mà plugin phát huy tác dụng bao gồm:

1. Đăng ký một hoặc nhiều global component hay custom directive bằng [`app.component()`](/api/application#app-component) và [`app.directive()`](/api/application#app-directive).

2. Làm cho một tài nguyên có thể [inject](/guide/components/provide-inject) ở khắp ứng dụng bằng cách gọi [`app.provide()`](/api/application#app-provide).

3. Thêm một vài property hoặc phương thức global vào instance bằng cách gắn chúng vào [`app.config.globalProperties`](/api/application#app-config-globalproperties).

4. Một thư viện cần thực hiện kết hợp của các điều trên (ví dụ [vue-router](https://github.com/vuejs/vue-router-next)).

## Viết Một Plugin {#writing-a-plugin}

Để hiểu rõ hơn cách tạo plugin Vue.js của riêng mình, ta sẽ xây dựng một phiên bản rất đơn giản của một plugin dùng để hiển thị chuỗi `i18n` (viết tắt của [Internationalization](https://en.wikipedia.org/wiki/Internationalization_and_localization)).

Hãy bắt đầu bằng cách tạo object plugin. Bạn nên tạo nó trong một file riêng và export ra, như ví dụ dưới đây, để giữ logic được tách biệt và gọn gàng.

```js [plugins/i18n.js]
export default {
  install: (app, options) => {
    // Mã plugin viết ở đây
  }
}
```

Ta muốn tạo một hàm dịch. Hàm này sẽ nhận một chuỗi `key` phân tách bằng dấu chấm, và ta sẽ dùng nó để tra cứu chuỗi đã dịch trong option do người dùng cung cấp. Cách dùng mong muốn trong template là:

```vue-html
<h1>{{ $translate('greetings.hello') }}</h1>
```

Vì hàm này nên khả dụng toàn cục trong mọi template, ta sẽ làm điều đó bằng cách gắn nó vào `app.config.globalProperties` trong plugin:

```js [plugins/i18n.js]
export default {
  install: (app, options) => {
    // đưa vào một phương thức $translate() khả dụng toàn cục
    app.config.globalProperties.$translate = (key) => {
      // lấy một property lồng nhau trong `options`
      // bằng cách dùng `key` làm đường dẫn
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```

Hàm `$translate` của ta sẽ nhận một chuỗi như `greetings.hello`, tìm trong cấu hình do người dùng cung cấp và trả về giá trị đã dịch.

Object chứa các chuỗi đã dịch nên được truyền vào plugin khi cài đặt thông qua đối số bổ sung của `app.use()`:

```js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```

Bây giờ, biểu thức ban đầu `$translate('greetings.hello')` sẽ được thay bằng `Bonjour!` khi chạy.

Xem thêm: [Augmenting Global Properties](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

:::tip
Hãy dùng global properties một cách tiết chế, vì mọi thứ sẽ nhanh chóng trở nên khó hiểu nếu quá nhiều global property do các plugin khác nhau đưa vào được sử dụng khắp ứng dụng.
:::

### Provide / Inject Với Plugin {#provide-inject-with-plugins}

Plugin cũng cho phép ta dùng `provide` để giúp người dùng plugin truy cập được một hàm hoặc thuộc tính. Ví dụ, ta có thể cho ứng dụng quyền truy cập vào đối số `options` để dùng object chứa bản dịch.

```js [plugins/i18n.js]
export default {
  install: (app, options) => {
    app.provide('i18n', options)
  }
}
```

Người dùng plugin lúc này có thể inject option của plugin vào component của họ bằng key `i18n`:

<div class="composition-api">

```vue
<script setup>
import { inject } from 'vue'

const i18n = inject('i18n')

console.log(i18n.greetings.hello)
</script>
```

</div>
<div class="options-api">

```js
export default {
  inject: ['i18n'],
  created() {
    console.log(this.i18n.greetings.hello)
  }
}
```

</div>

### Đóng Gói Để Phát Hành Trên NPM {#bundle-for-npm}

Nếu bạn muốn tiếp tục build và phát hành plugin của mình để người khác dùng, hãy xem [phần Library Mode của Vite](https://vite.dev/guide/build.html#library-mode).
