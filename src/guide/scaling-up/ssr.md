---
outline: deep
---

# Server-Side Rendering (SSR) {#server-side-rendering-ssr}

## Tổng Quan {#overview}

### SSR Là Gì? {#what-is-ssr}

Vue.js là framework để xây ứng dụng phía client. Mặc định, component Vue tạo và thao tác DOM trong trình duyệt để cho ra giao diện. Tuy nhiên, ta cũng có thể render chính những component đó thành chuỗi HTML ở phía server, gửi thẳng xuống trình duyệt, rồi cuối cùng "hydrate" phần markup tĩnh đó thành một ứng dụng tương tác đầy đủ ở phía client.

Một ứng dụng Vue.js được render ở phía server cũng có thể được xem là "isomorphic" hoặc "universal", theo nghĩa là phần lớn mã của ứng dụng chạy trên cả server **lẫn** client.

### Vì Sao Dùng SSR? {#why-ssr}

So với Single-Page Application (SPA) chạy thuần phía client, lợi thế chính của SSR nằm ở:

- **Time-to-content nhanh hơn**: điều này thể hiện rõ hơn trên mạng chậm hoặc thiết bị chậm. Markup render từ server không cần chờ cho tới khi toàn bộ JavaScript được tải xuống và thực thi mới hiển thị, nên người dùng sẽ thấy trang đã render hoàn chỉnh sớm hơn. Ngoài ra, việc fetch dữ liệu cho lần truy cập đầu tiên diễn ra ở phía server, nơi thường có kết nối tới cơ sở dữ liệu nhanh hơn client. Điều này thường cải thiện các chỉ số [Core Web Vitals](https://web.dev/vitals/), nâng trải nghiệm người dùng, và có thể rất quan trọng với những ứng dụng mà time-to-content gắn trực tiếp với tỷ lệ chuyển đổi.

- **Mô hình tư duy thống nhất**: bạn có thể dùng cùng một ngôn ngữ và cùng một mô hình khai báo, hướng component để phát triển toàn bộ ứng dụng, thay vì phải liên tục chuyển qua lại giữa hệ template phía backend và framework frontend.

- **SEO tốt hơn**: crawler của công cụ tìm kiếm sẽ nhìn thấy ngay trang đã được render hoàn chỉnh.

  :::tip
  Hiện tại, Google và Bing vẫn có thể đánh chỉ mục tốt cho ứng dụng JavaScript đồng bộ. Từ khóa ở đây là đồng bộ. Nếu ứng dụng của bạn bắt đầu bằng spinner loading, rồi mới fetch nội dung qua Ajax, crawler sẽ không chờ bạn hoàn tất. Điều đó có nghĩa là nếu bạn có nội dung được lấy bất đồng bộ trên những trang quan trọng với SEO, SSR có thể là cần thiết.
  :::

SSR cũng đi kèm một số đánh đổi cần cân nhắc:

- Ràng buộc khi phát triển. Mã riêng cho trình duyệt chỉ có thể dùng trong một số hook vòng đời nhất định. Một vài thư viện bên ngoài cũng có thể cần xử lý đặc biệt để chạy được trong ứng dụng render phía server.

- Thiết lập build và yêu cầu triển khai phức tạp hơn. Không giống SPA tĩnh hoàn toàn có thể triển khai trên bất kỳ static file server nào, ứng dụng render phía server cần môi trường có thể chạy Node.js server.

- Tải phía server nhiều hơn. Việc render cả ứng dụng trong Node.js sẽ tốn CPU hơn đáng kể so với chỉ phục vụ file tĩnh. Vì vậy, nếu bạn kỳ vọng lưu lượng truy cập cao, hãy chuẩn bị cho tải phía server tương ứng và áp dụng chiến lược cache hợp lý.

Trước khi dùng SSR cho ứng dụng, câu hỏi đầu tiên bạn nên đặt ra là liệu bạn có thực sự cần nó hay không. Điều này chủ yếu phụ thuộc vào mức độ quan trọng của time-to-content đối với ứng dụng của bạn. Ví dụ, nếu bạn đang xây một dashboard nội bộ nơi việc tải lần đầu chậm hơn vài trăm mili giây không quan trọng lắm, SSR có thể là quá mức cần thiết. Nhưng trong những trường hợp mà time-to-content thực sự mang tính quyết định, SSR sẽ giúp bạn đạt được hiệu năng tải ban đầu tốt nhất có thể.

### SSR So Với SSG {#ssr-vs-ssg}

**Static Site Generation (SSG)**, còn được gọi là pre-rendering, là một kỹ thuật phổ biến khác để xây website nhanh. Nếu dữ liệu cần để render một trang ở phía server là giống nhau với mọi người dùng, thì thay vì render lại trang mỗi khi có request, ta có thể render nó một lần duy nhất từ trước, trong quá trình build. Các trang được pre-render sẽ được sinh ra và phục vụ dưới dạng file HTML tĩnh.

SSG giữ lại các đặc điểm hiệu năng tương tự ứng dụng SSR: time-to-content rất tốt. Đồng thời, nó rẻ hơn và dễ triển khai hơn SSR vì đầu ra chỉ là HTML tĩnh và các asset. Từ khóa quan trọng ở đây là **tĩnh**: SSG chỉ áp dụng được cho các trang cung cấp dữ liệu tĩnh, tức dữ liệu đã biết tại thời điểm build và không thay đổi giữa các request. Mỗi khi dữ liệu thay đổi, bạn cần triển khai lại.

Nếu bạn chỉ đang cân nhắc SSR để cải thiện SEO cho một vài trang marketing như `/`, `/about`, `/contact`, v.v. thì có lẽ bạn sẽ muốn SSG hơn là SSR. SSG cũng rất phù hợp cho website thiên về nội dung như trang tài liệu hoặc blog. Thực tế, chính website bạn đang đọc này được sinh tĩnh bằng [VitePress](https://vitepress.dev/), một static site generator dùng Vue.

## Hướng Dẫn Cơ Bản {#basic-tutorial}

### Render Một Ứng Dụng {#rendering-an-app}

Hãy xem ví dụ tối giản nhất về Vue SSR.

1. Tạo một thư mục mới rồi `cd` vào đó
2. Chạy `npm init -y`
3. Thêm `"type": "module"` vào `package.json` để Node.js chạy ở [chế độ ES modules](https://nodejs.org/api/esm.html#modules-ecmascript-modules)
4. Chạy `npm install vue`
5. Tạo file `example.js` như sau:

```js
// đoạn này chạy trong Node.js ở phía server.
import { createSSRApp } from 'vue'
// API render phía server của Vue được lộ qua `vue/server-renderer`.
import { renderToString } from 'vue/server-renderer'

const app = createSSRApp({
  data: () => ({ count: 1 }),
  template: `<button @click="count++">{{ count }}</button>`
})

renderToString(app).then((html) => {
  console.log(html)
})
```

Sau đó chạy:

```sh
> node example.js
```

Nó sẽ in ra dòng sau trên command line:

```
<button>1</button>
```

[`renderToString()`](/api/ssr#rendertostring) nhận một instance ứng dụng Vue và trả về Promise resolve ra HTML đã render của ứng dụng. Bạn cũng có thể stream quá trình render bằng [Node.js Stream API](https://nodejs.org/api/stream.html) hoặc [Web Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API). Xem [SSR API Reference](/api/ssr) để biết đầy đủ chi tiết.

Sau đó, ta có thể đưa phần mã Vue SSR vào một request handler của server, nơi sẽ bọc markup của ứng dụng bằng HTML hoàn chỉnh của cả trang. Trong các bước tiếp theo, ta sẽ dùng [`express`](https://expressjs.com/):

- Chạy `npm install express`
- Tạo file `server.js` như sau:

```js
import express from 'express'
import { createSSRApp } from 'vue'
import { renderToString } from 'vue/server-renderer'

const server = express()

server.get('/', (req, res) => {
  const app = createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })

  renderToString(app).then((html) => {
    res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>Vue SSR Example</title>
      </head>
      <body>
        <div id="app">${html}</div>
      </body>
    </html>
    `)
  })
})

server.listen(3000, () => {
  console.log('ready')
})
```

Cuối cùng, chạy `node server.js` rồi truy cập `http://localhost:3000`. Bạn sẽ thấy trang hiển thị với nút bấm.

[Thử trên StackBlitz](https://stackblitz.com/fork/vue-ssr-example-basic?file=index.js)

### Client Hydration {#client-hydration}

Nếu bạn bấm vào nút, bạn sẽ thấy con số không thay đổi. HTML phía client hiện hoàn toàn tĩnh vì ta chưa nạp Vue trong trình duyệt.

Để ứng dụng phía client trở nên tương tác được, Vue cần thực hiện bước **hydration**. Trong quá trình hydration, Vue tạo lại đúng ứng dụng Vue đã chạy ở phía server, ghép từng component với node DOM mà nó cần kiểm soát và gắn listener sự kiện DOM.

Để mount ứng dụng ở chế độ hydration, ta cần dùng [`createSSRApp()`](/api/application#createssrapp) thay vì `createApp()`:

```js{2}
// đoạn này chạy trong trình duyệt.
import { createSSRApp } from 'vue'

const app = createSSRApp({
  // ...cùng ứng dụng như ở server
})

// việc mount ứng dụng SSR ở client giả định rằng
// HTML đã được render sẵn, nên nó sẽ hydration
// thay vì mount các node DOM mới.
app.mount('#app')
```

### Cấu Trúc Mã {#code-structure}

Hãy để ý rằng ta cần dùng lại cùng một phần cài đặt ứng dụng ở cả server lẫn client. Đây là lúc ta phải nghĩ tới cấu trúc mã trong ứng dụng SSR: làm sao để chia sẻ cùng một mã ứng dụng giữa server và client?

Ở đây ta sẽ minh họa thiết lập tối giản nhất. Trước hết, hãy tách logic tạo ứng dụng vào một file riêng là `app.js`:

```js [app.js]
// (dùng chung cho server và client)
import { createSSRApp } from 'vue'

export function createApp() {
  return createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })
}
```

File này và các dependency của nó được chia sẻ giữa server và client. Ta gọi chúng là **universal code**. Có một số điều bạn cần chú ý khi viết universal code, như ta sẽ [bàn bên dưới](#writing-ssr-friendly-code).

Entry phía client import phần universal code, tạo ứng dụng và mount:

```js [client.js]
import { createApp } from './app.js'

createApp().mount('#app')
```

Và phía server dùng cùng logic tạo ứng dụng đó trong request handler:

```js{2,5} [server.js]
// (lược bỏ mã không liên quan)
import { createApp } from './app.js'

server.get('/', (req, res) => {
  const app = createApp()
  renderToString(app).then(html => {
    // ...
  })
})
```

Ngoài ra, để nạp được file client trong trình duyệt, ta còn cần:

1. Phục vụ file client bằng cách thêm `server.use(express.static('.'))` vào `server.js`.
2. Nạp entry client bằng cách thêm `<script type="module" src="/client.js"></script>` vào HTML shell.
3. Hỗ trợ cách dùng như `import * from 'vue'` trong trình duyệt bằng cách thêm [Import Map](https://github.com/WICG/import-maps) vào HTML shell.

[Thử ví dụ hoàn chỉnh trên StackBlitz](https://stackblitz.com/fork/vue-ssr-example?file=index.js). Giờ nút bấm đã tương tác được.

## Các Giải Pháp Ở Mức Cao Hơn {#higher-level-solutions}

Đi từ ví dụ trên tới một ứng dụng SSR sẵn sàng cho production sẽ phức tạp hơn nhiều. Ta sẽ cần:

- Hỗ trợ Vue SFC và những yêu cầu build step khác. Trên thực tế, ta sẽ cần phối hợp hai lần build cho cùng một ứng dụng: một cho client và một cho server.

  :::tip
  Component Vue được biên dịch khác đi khi dùng cho SSR: template sẽ được biên dịch thành phép nối chuỗi thay vì hàm render virtual DOM để render hiệu quả hơn.
  :::

- Trong request handler của server, render HTML với liên kết asset phía client chính xác và gợi ý tài nguyên tối ưu. Ta cũng có thể cần chuyển giữa SSR và SSG, hoặc thậm chí trộn cả hai trong cùng một ứng dụng.

- Quản lý routing, fetch dữ liệu và store quản lý state theo cách dùng chung cho cả server và client.

Một cài đặt đầy đủ sẽ khá phức tạp và phụ thuộc vào toolchain build mà bạn chọn. Vì vậy, chúng tôi rất khuyến nghị dùng một giải pháp ở mức cao hơn, có quan điểm rõ ràng, để che đi phần phức tạp này. Dưới đây là một vài giải pháp SSR được khuyến nghị trong hệ sinh thái Vue.

### Nuxt {#nuxt}

[Nuxt](https://nuxt.com/) là framework ở mức cao hơn được xây trên hệ sinh thái Vue, cung cấp trải nghiệm phát triển mượt mà cho việc viết ứng dụng Vue universal. Tốt hơn nữa, bạn cũng có thể dùng nó như một static site generator. Chúng tôi rất khuyến nghị bạn thử Nuxt.

### Quasar {#quasar}

[Quasar](https://quasar.dev) là giải pháp Vue toàn diện cho phép bạn nhắm tới SPA, SSR, PWA, mobile app, desktop app và browser extension chỉ từ một codebase. Nó không chỉ xử lý thiết lập build mà còn cung cấp cả bộ component UI tuân theo Material Design.

### Vite SSR {#vite-ssr}

Vite có [hỗ trợ sẵn cho Vue server-side rendering](https://vite.dev/guide/ssr.html), nhưng nó cố ý ở mức thấp. Nếu bạn muốn đi thẳng với Vite, hãy xem [vite-plugin-ssr](https://vite-plugin-ssr.com/), một plugin cộng đồng giúp trừu tượng hóa nhiều chi tiết khó cho bạn.

Bạn cũng có thể xem dự án mẫu Vue + Vite SSR dùng thiết lập thủ công [ở đây](https://github.com/vitejs/vite-plugin-vue/tree/main/playground/ssr-vue), có thể làm nền tảng để xây tiếp. Tuy nhiên, cách này chỉ thật sự được khuyến nghị nếu bạn đã có kinh nghiệm với SSR / build tool và thực sự muốn kiểm soát hoàn toàn kiến trúc ở mức cao hơn.

## Viết Mã Thân Thiện Với SSR {#writing-ssr-friendly-code}

Dù bạn dùng thiết lập build nào hay framework mức cao nào, vẫn có một số nguyên tắc chung áp dụng cho mọi ứng dụng Vue SSR.

### Tính Phản Ứng Ở Phía Server {#reactivity-on-the-server}

Trong SSR, mỗi URL request tương ứng với một trạng thái mong muốn của ứng dụng. Không có tương tác người dùng và cũng không có cập nhật DOM, nên tính phản ứng là không cần thiết ở phía server. Mặc định, reactivity bị tắt trong SSR để đạt hiệu năng tốt hơn.

### Hook Vòng Đời Component {#component-lifecycle-hooks}

Vì không có cập nhật động, các hook vòng đời như <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> hoặc <span class="options-api">`updated`</span><span class="composition-api">`onUpdated`</span> sẽ **KHÔNG** được gọi trong SSR và chỉ chạy ở phía client.<span class="options-api"> Các hook duy nhất được gọi trong SSR là `beforeCreate` và `created`.</span>

Bạn nên tránh viết mã tạo ra side effect cần được dọn dẹp trong <span class="options-api">`beforeCreate` và `created`</span><span class="composition-api">`setup()` hoặc phạm vi gốc của `<script setup>`</span>. Ví dụ của loại side effect này là tạo timer bằng `setInterval`. Trong mã chỉ chạy phía client, ta có thể tạo timer rồi dọn dẹp trong <span class="options-api">`beforeUnmount`</span><span class="composition-api">`onBeforeUnmount`</span> hoặc <span class="options-api">`unmounted`</span><span class="composition-api">`onUnmounted`</span>. Tuy nhiên, vì hook unmount sẽ không bao giờ được gọi trong SSR, timer đó sẽ tồn tại mãi. Để tránh điều này, hãy chuyển mã side effect vào <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span>.

### Truy Cập API Đặc Thù Nền Tảng {#access-to-platform-specific-apis}

Universal code không thể giả định rằng luôn có sẵn API đặc thù của nền tảng. Vì vậy, nếu mã của bạn truy cập trực tiếp các global chỉ có trong trình duyệt như `window` hoặc `document`, chúng sẽ ném lỗi khi chạy trong Node.js, và ngược lại.

Với những tác vụ được chia sẻ giữa server và client nhưng dùng API nền tảng khác nhau, bạn nên bọc phần cài đặt đặc thù nền tảng trong một API dùng chung, hoặc dùng thư viện đã làm việc đó sẵn cho bạn. Ví dụ, bạn có thể dùng [`node-fetch`](https://github.com/node-fetch/node-fetch) để có cùng fetch API ở cả server lẫn client.

Với các API chỉ có trong trình duyệt, cách làm phổ biến là truy cập chúng một cách lazy bên trong các hook chỉ chạy phía client như <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span>.

Lưu ý rằng nếu một thư viện bên thứ ba không được viết với tinh thần dùng chung, việc tích hợp nó vào ứng dụng render phía server có thể khá khó. Bạn _có thể_ làm nó hoạt động bằng cách giả lập một số global, nhưng đó sẽ là giải pháp chắp vá và có thể làm ảnh hưởng tới cơ chế phát hiện môi trường của những thư viện khác.

### Ô Nhiễm State Giữa Các Request {#cross-request-state-pollution}

Trong chương Quản Lý State, ta đã giới thiệu [một pattern quản lý state đơn giản bằng Reactivity API](state-management#simple-state-management-with-reactivity-api). Trong bối cảnh SSR, pattern này cần thêm một vài điều chỉnh.

Pattern đó khai báo state dùng chung ở phạm vi gốc của module JavaScript. Điều này biến chúng thành **singleton**, tức chỉ có một instance của object reactive trong suốt vòng đời của ứng dụng. Điều này hoạt động đúng như mong đợi trong ứng dụng Vue chạy thuần phía client, vì module của ứng dụng được khởi tạo lại từ đầu cho mỗi lần tải trang trong trình duyệt.

Tuy nhiên, trong bối cảnh SSR, các module của ứng dụng thường chỉ được khởi tạo một lần trên server khi server khởi động. Các instance module đó sẽ được tái sử dụng qua nhiều request, kéo theo các object state singleton của ta cũng vậy. Nếu ta thay đổi singleton state dùng chung bằng dữ liệu riêng của một người dùng, dữ liệu đó có thể vô tình bị lộ sang request của người dùng khác. Ta gọi đây là **ô nhiễm state giữa các request**.

Về mặt kỹ thuật, ta có thể khởi tạo lại toàn bộ module JavaScript trên mỗi request, giống như cách trình duyệt làm. Tuy nhiên, việc khởi tạo module JavaScript có chi phí, nên điều này sẽ ảnh hưởng đáng kể đến hiệu năng của server.

Giải pháp được khuyến nghị là tạo instance mới cho toàn bộ ứng dụng trên mỗi request, bao gồm router và global store. Sau đó, thay vì import trực tiếp trong component, ta cung cấp state dùng chung thông qua [provide ở cấp ứng dụng](/guide/components/provide-inject#app-level-provide) và inject nó trong những component cần dùng:

```js [app.js]
// (dùng chung cho server và client)
import { createSSRApp } from 'vue'
import { createStore } from './store.js'

// được gọi cho mỗi request
export function createApp() {
  const app = createSSRApp(/* ... */)
  // tạo instance store mới cho từng request
  const store = createStore(/* ... */)
  // provide store ở cấp ứng dụng
  app.provide('store', store)
  // cũng lộ store ra để phục vụ hydration
  return { app, store }
}
```

Các thư viện quản lý state như Pinia được thiết kế với điều này trong đầu. Hãy xem [hướng dẫn SSR của Pinia](https://pinia.vuejs.org/ssr/) để biết thêm chi tiết.

### Sai Lệch Hydration {#hydration-mismatch}

Nếu cấu trúc DOM của HTML đã render sẵn không khớp với đầu ra mà ứng dụng phía client mong đợi, sẽ xảy ra lỗi hydration mismatch. Hydration mismatch thường xuất phát từ các nguyên nhân sau:

1. Template chứa cấu trúc lồng HTML không hợp lệ, và HTML đã render bị trình duyệt "sửa lại" theo hành vi phân tích cú pháp HTML gốc. Ví dụ, một bẫy phổ biến là [`<div>` không thể nằm trong `<p>`](https://stackoverflow.com/questions/8397852/why-cant-the-p-tag-contain-a-div-tag-inside-it):

   ```html
   <p><div>hi</div></p>
   ```

   Nếu ta tạo ra HTML render phía server như vậy, trình duyệt sẽ đóng `<p>` đầu tiên ngay khi gặp `<div>` và phân tích nó thành cấu trúc DOM như sau:

   ```html
   <p></p>
   <div>hi</div>
   <p></p>
   ```

2. Dữ liệu dùng trong lúc render chứa các giá trị sinh ngẫu nhiên. Vì cùng một ứng dụng sẽ chạy hai lần, một lần ở server và một lần ở client, các giá trị ngẫu nhiên không được bảo đảm là giống nhau giữa hai lần chạy. Có hai cách tránh mismatch do giá trị ngẫu nhiên:

   1. Dùng `v-if` + `onMounted` để chỉ render phần phụ thuộc vào giá trị ngẫu nhiên ở phía client. Framework của bạn cũng có thể có sẵn tính năng hỗ trợ việc này, chẳng hạn component `<ClientOnly>` trong VitePress.

   2. Dùng thư viện random number generator hỗ trợ seed, và bảo đảm lần chạy ở server và client cùng dùng một seed, ví dụ bằng cách đưa seed vào state đã serialize rồi lấy lại ở client.

3. Server và client ở hai múi giờ khác nhau. Đôi khi ta muốn chuyển timestamp sang giờ địa phương của người dùng. Tuy nhiên, múi giờ trong lần chạy ở server và lần chạy ở client không phải lúc nào cũng giống nhau, và ta cũng không thể luôn biết trước múi giờ của người dùng khi chạy ở server. Trong những trường hợp như vậy, việc chuyển sang giờ địa phương cũng nên được thực hiện như thao tác chỉ chạy phía client.

Khi Vue gặp hydration mismatch, nó sẽ cố gắng tự động khôi phục và điều chỉnh DOM đã render sẵn để khớp với state phía client. Điều này sẽ gây tổn thất hiệu năng render do một số node sai bị loại bỏ và node mới được mount vào, nhưng trong đa số trường hợp, ứng dụng vẫn sẽ tiếp tục hoạt động như mong đợi. Dù vậy, tốt nhất bạn vẫn nên loại bỏ hydration mismatch trong quá trình phát triển.

#### Chặn Hiển Thị Cảnh Báo Hydration Mismatch <sup class="vt-badge" data-text="3.5+" /> {#suppressing-hydration-mismatches}

Trong Vue 3.5+, bạn có thể chọn lọc bỏ qua những hydration mismatch không thể tránh được bằng thuộc tính [`data-allow-mismatch`](/api/ssr#data-allow-mismatch).

### Custom Directive {#custom-directives}

Vì phần lớn custom directive liên quan đến thao tác DOM trực tiếp, chúng sẽ bị bỏ qua trong SSR. Tuy nhiên, nếu bạn muốn chỉ định custom directive nên được render như thế nào, tức là nó cần thêm attribute gì vào phần tử được render, bạn có thể dùng hook `getSSRProps` của directive:

```js
const myDirective = {
  mounted(el, binding) {
    // cài đặt phía client:
    // cập nhật DOM trực tiếp
    el.id = binding.value
  },
  getSSRProps(binding) {
    // cài đặt phía server:
    // trả về props cần được render.
    // getSSRProps chỉ nhận directive binding.
    return {
      id: binding.value
    }
  }
}
```

### Teleport {#teleports}

Teleport cần được xử lý đặc biệt trong SSR. Nếu ứng dụng đã render có chứa Teleport, nội dung được teleport sẽ không nằm trong chuỗi render chính. Giải pháp đơn giản hơn là chỉ render Teleport sau khi mount.

Nếu bạn thực sự cần hydrate nội dung teleport, chúng sẽ được lộ ra trong property `teleports` của object ngữ cảnh SSR:

```js
const ctx = {}
const html = await renderToString(app, ctx)

console.log(ctx.teleports) // { '#teleported': 'teleported content' }
```

Bạn cần chèn markup teleport vào đúng vị trí trong HTML cuối cùng của trang, tương tự như cách bạn chèn markup của ứng dụng chính.

:::tip
Hãy tránh nhắm tới `body` khi dùng Teleport cùng SSR. Thông thường, `<body>` sẽ còn chứa nội dung render từ server khác, khiến Teleport không thể xác định chính xác vị trí bắt đầu để hydration.

Thay vào đó, hãy ưu tiên một phần tử chứa chuyên dụng, ví dụ `<div id="teleported"></div>`, nơi chỉ chứa nội dung teleport.
:::
