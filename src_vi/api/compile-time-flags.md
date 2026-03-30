---
outline: deep
---

# Flag Thời điểm Biên dịch {#compile-time-flags}

:::tip
Các flag thời điểm biên dịch chỉ áp dụng khi dùng bản build `esm-bundler` của Vue (tức là `vue/dist/vue.esm-bundler.js`).
:::

Khi dùng Vue với bước build, có thể cấu hình một số flag thời điểm biên dịch để bật/tắt một số tính năng nhất định. Lợi ích của việc dùng flag thời điểm biên dịch là các tính năng bị tắt theo cách này có thể bị loại bỏ khỏi bundle cuối cùng qua tree-shaking.

Vue vẫn hoạt động ngay cả khi các flag này không được cấu hình rõ ràng. Tuy nhiên, khuyến nghị luôn cấu hình chúng để các tính năng liên quan có thể được loại bỏ đúng cách khi có thể.

Xem [Hướng dẫn Cấu hình](#configuration-guides) về cách cấu hình chúng tùy theo build tool của bạn.

## `__VUE_OPTIONS_API__` {#VUE_OPTIONS_API}

- **Mặc định:** `true`

  Bật/tắt hỗ trợ Options API. Tắt tính năng này sẽ tạo ra bundle nhỏ hơn, nhưng có thể ảnh hưởng đến khả năng tương thích với các thư viện bên thứ ba nếu chúng phụ thuộc vào Options API.

## `__VUE_PROD_DEVTOOLS__` {#VUE_PROD_DEVTOOLS}

- **Mặc định:** `false`

  Bật/tắt hỗ trợ devtools trong bản build production. Điều này sẽ dẫn đến nhiều code hơn trong bundle, vì vậy chỉ khuyến nghị bật tính năng này cho mục đích debug.

## `__VUE_PROD_HYDRATION_MISMATCH_DETAILS__` {#VUE_PROD_HYDRATION_MISMATCH_DETAILS}

- **Mặc định:** `false`

  Bật/tắt cảnh báo chi tiết về sai lệch hydration trong bản build production. Điều này sẽ dẫn đến nhiều code hơn trong bundle, vì vậy chỉ khuyến nghị bật tính năng này cho mục đích debug.

- Chỉ có sẵn trong 3.4+

## Hướng dẫn Cấu hình {#configuration-guides}

### Vite {#vite}

`@vitejs/plugin-vue` tự động cung cấp các giá trị mặc định cho các flag này. Để thay đổi giá trị mặc định, dùng [tùy chọn cấu hình `define`](https://vite.dev/config/shared-options.html#define) của Vite:

```js [vite.config.js]
import { defineConfig } from 'vite'

export default defineConfig({
  define: {
    // bật chi tiết sai lệch hydration trong bản build production
    __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'true'
  }
})
```

### vue-cli {#vue-cli}

`@vue/cli-service` tự động cung cấp các giá trị mặc định cho một số flag này. Để cấu hình/thay đổi các giá trị:

```js [vue.config.js]
module.exports = {
  chainWebpack: (config) => {
    config.plugin('define').tap((definitions) => {
      Object.assign(definitions[0], {
        __VUE_OPTIONS_API__: 'true',
        __VUE_PROD_DEVTOOLS__: 'false',
        __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
      })
      return definitions
    })
  }
}
```

### webpack {#webpack}

Các flag nên được định nghĩa bằng [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) của webpack:

```js [webpack.config.js]
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```

### Rollup {#rollup}

Các flag nên được định nghĩa bằng [@rollup/plugin-replace](https://github.com/rollup/plugins/tree/master/packages/replace):

```js [rollup.config.js]
import replace from '@rollup/plugin-replace'

export default {
  plugins: [
    replace({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```
