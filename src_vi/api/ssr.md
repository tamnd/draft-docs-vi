# API Kết xuất Phía Máy chủ {#server-side-rendering-api}

## renderToString() {#rendertostring}

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function renderToString(
    input: App | VNode,
    context?: SSRContext
  ): Promise<string>
  ```

- **Ví dụ**

  ```js
  import { createSSRApp } from 'vue'
  import { renderToString } from 'vue/server-renderer'

  const app = createSSRApp({
    data: () => ({ msg: 'hello' }),
    template: `<div>{{ msg }}</div>`
  })

  ;(async () => {
    const html = await renderToString(app)
    console.log(html)
  })()
  ```

  ### SSR Context {#ssr-context}

  Bạn có thể truyền một object context tùy chọn, có thể dùng để ghi lại dữ liệu bổ sung trong quá trình render, ví dụ [truy cập nội dung của Teleport](/guide/scaling-up/ssr#teleports):

  ```js
  const ctx = {}
  const html = await renderToString(app, ctx)

  console.log(ctx.teleports) // { '#teleported': 'teleported content' }
  ```

  Hầu hết các SSR API khác trên trang này cũng tùy chọn nhận một context object. Context object có thể được truy cập trong code component qua helper [useSSRContext](#usessrcontext).

- **Xem thêm** [Hướng dẫn - Kết xuất Phía Máy chủ](/guide/scaling-up/ssr)

## renderToNodeStream() {#rendertonodestream}

Render input dưới dạng [Node.js Readable stream](https://nodejs.org/api/stream.html#stream_class_stream_readable).

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function renderToNodeStream(
    input: App | VNode,
    context?: SSRContext
  ): Readable
  ```

- **Ví dụ**

  ```js
  // bên trong Node.js http handler
  renderToNodeStream(app).pipe(res)
  ```

  :::tip Lưu ý
  Method này không được hỗ trợ trong bản build ESM của `vue/server-renderer`, tách rời khỏi môi trường Node.js. Dùng [`pipeToNodeWritable`](#pipetonodewritable) thay thế.
  :::

## pipeToNodeWritable() {#pipetonodewritable}

Render và pipe đến một instance [Node.js Writable stream](https://nodejs.org/api/stream.html#stream_writable_streams) hiện có.

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function pipeToNodeWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: Writable
  ): void
  ```

- **Ví dụ**

  ```js
  // bên trong Node.js http handler
  pipeToNodeWritable(app, {}, res)
  ```

## renderToWebStream() {#rendertowebstream}

Render input dưới dạng [Web ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API).

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function renderToWebStream(
    input: App | VNode,
    context?: SSRContext
  ): ReadableStream
  ```

- **Ví dụ**

  ```js
  // bên trong môi trường có hỗ trợ ReadableStream
  return new Response(renderToWebStream(app))
  ```

  :::tip Lưu ý
  Trong các môi trường không expose constructor `ReadableStream` trong global scope, nên dùng [`pipeToWebWritable()`](#pipetowebwritable) thay thế.
  :::

## pipeToWebWritable() {#pipetowebwritable}

Render và pipe đến một instance [Web WritableStream](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream) hiện có.

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function pipeToWebWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: WritableStream
  ): void
  ```

- **Ví dụ**

  Thường được dùng kết hợp với [`TransformStream`](https://developer.mozilla.org/en-US/docs/Web/API/TransformStream):

  ```js
  // TransformStream có sẵn trong các môi trường như CloudFlare workers.
  // trong Node.js, TransformStream cần được import rõ ràng từ 'stream/web'
  const { readable, writable } = new TransformStream()
  pipeToWebWritable(app, {}, writable)

  return new Response(readable)
  ```

## renderToSimpleStream() {#rendertosimplestream}

Render input ở chế độ streaming bằng cách dùng interface readable đơn giản.

- **Xuất từ `vue/server-renderer`**

- **Kiểu**

  ```ts
  function renderToSimpleStream(
    input: App | VNode,
    context: SSRContext,
    options: SimpleReadable
  ): SimpleReadable

  interface SimpleReadable {
    push(content: string | null): void
    destroy(err: any): void
  }
  ```

- **Ví dụ**

  ```js
  let res = ''

  renderToSimpleStream(
    app,
    {},
    {
      push(chunk) {
        if (chunk === null) {
          // xong
          console(`render complete: ${res}`)
        } else {
          res += chunk
        }
      },
      destroy(err) {
        // gặp lỗi
      }
    }
  )
  ```

## useSSRContext() {#usessrcontext}

Một runtime API dùng để lấy context object được truyền vào `renderToString()` hoặc các server render API khác.

- **Kiểu**

  ```ts
  function useSSRContext<T = Record<string, any>>(): T | undefined
  ```

- **Ví dụ**

  Context được lấy có thể dùng để đính kèm thông tin cần thiết để render HTML cuối cùng (ví dụ: head metadata).

  ```vue
  <script setup>
  import { useSSRContext } from 'vue'

  // đảm bảo chỉ gọi trong SSR
  // https://vite.dev/guide/ssr.html#conditional-logic
  if (import.meta.env.SSR) {
    const ctx = useSSRContext()
    // ...đính kèm thuộc tính vào context
  }
  </script>
  ```

## data-allow-mismatch <sup class="vt-badge" data-text="3.5+" /> {#data-allow-mismatch}

Thuộc tính đặc biệt có thể dùng để bỏ qua cảnh báo [hydration mismatch](/guide/scaling-up/ssr#hydration-mismatch).

- **Ví dụ**

  ```html
  <div data-allow-mismatch="text">{{ data.toLocaleString() }}</div>
  ```

  Giá trị có thể giới hạn mismatch được phép theo loại cụ thể. Các giá trị được phép là:

  - `text`
  - `children` (chỉ cho phép mismatch cho direct children)
  - `class`
  - `style`
  - `attribute`

  Nếu không cung cấp giá trị, tất cả loại mismatch sẽ được cho phép.
