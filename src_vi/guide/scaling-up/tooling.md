<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# Công Cụ Phát Triển {#tooling}

## Dùng Thử Trực Tuyến {#try-it-online}

Bạn không cần cài gì trên máy để thử Vue SFC. Có nhiều playground trực tuyến cho phép bạn làm điều đó ngay trong trình duyệt:

- [Vue SFC Playground](https://play.vuejs.org)
  - Luôn được triển khai từ commit mới nhất
  - Phù hợp để xem kết quả biên dịch component
- [Vue + Vite on StackBlitz](https://vite.new/vue)
  - Môi trường giống IDE chạy dev server Vite thật ngay trong trình duyệt
  - Gần nhất với thiết lập cục bộ

Bạn cũng nên dùng các playground trực tuyến này để tạo bản tái hiện khi báo lỗi.

## Khởi Tạo Dự Án {#project-scaffolding}

### Vite {#vite}

[Vite](https://vite.dev/) là một build tool nhẹ và nhanh, hỗ trợ Vue SFC ở mức hạng nhất. Nó được tạo bởi Evan You, cũng chính là tác giả của Vue.

Để bắt đầu với Vite + Vue, chỉ cần chạy:

::: code-group

```sh [npm]
$ npm create vue@latest
```

```sh [pnpm]
$ pnpm create vue@latest
```

```sh [yarn]
# Với Yarn Modern (v2+)
$ yarn create vue@latest

# Với Yarn ^v4.11
$ yarn dlx create-vue@latest
```

```sh [bun]
$ bun create vue@latest
```

:::

Lệnh này sẽ cài và chạy [create-vue](https://github.com/vuejs/create-vue), công cụ khởi tạo dự án chính thức của Vue.

- Để tìm hiểu thêm về Vite, xem [tài liệu Vite](https://vite.dev/).
- Để cấu hình hành vi riêng cho Vue trong dự án Vite, ví dụ truyền option cho Vue compiler, xem tài liệu của [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#readme).

Hai playground trực tuyến ở trên cũng hỗ trợ tải file về dưới dạng dự án Vite.

### Vue CLI {#vue-cli}

[Vue CLI](https://cli.vuejs.org/) là bộ công cụ chính thức dựa trên webpack của Vue. Hiện tại nó đang ở chế độ bảo trì, và chúng tôi khuyến nghị bắt đầu dự án mới với Vite trừ khi bạn phụ thuộc vào tính năng chỉ webpack mới có. Trong phần lớn trường hợp, Vite sẽ mang lại trải nghiệm phát triển tốt hơn.

Thông tin về việc chuyển từ Vue CLI sang Vite:

- [Vue CLI -> Vite Migration Guide from VueSchool.io](https://vueschool.io/articles/vuejs-tutorials/how-to-migrate-from-vue-cli-to-vite/)
- [Tools / Plugins that help with auto migration](https://github.com/vitejs/awesome-vite#vue-cli)

### Lưu Ý Về Biên Dịch Template Trong Trình Duyệt {#note-on-in-browser-template-compilation}

Khi dùng Vue mà không có build step, template của component sẽ được viết trực tiếp trong HTML của trang hoặc dưới dạng chuỗi JavaScript inline. Trong các trường hợp đó, Vue phải mang theo template compiler lên trình duyệt để biên dịch template ngay lúc chạy. Ngược lại, compiler là không cần thiết nếu ta biên dịch trước template bằng build step. Để giảm kích thước bundle phía client, Vue cung cấp [nhiều "bản build"](https://unpkg.com/browse/vue@3/dist/) tối ưu cho các nhu cầu sử dụng khác nhau.

- Các file build bắt đầu bằng `vue.runtime.*` là **runtime-only build**: chúng không bao gồm compiler. Khi dùng các bản build này, mọi template phải được biên dịch trước thông qua build step.

- Các file build không chứa `.runtime` là **full build**: chúng có compiler và hỗ trợ biên dịch template trực tiếp trong trình duyệt. Tuy nhiên, chúng sẽ làm payload tăng khoảng ~14kb.

Thiết lập công cụ mặc định của chúng ta dùng runtime-only build vì toàn bộ template trong SFC đều đã được biên dịch trước. Nếu vì lý do nào đó bạn vẫn cần biên dịch template trong trình duyệt ngay cả khi có build step, bạn có thể cấu hình build tool để alias `vue` sang `vue/dist/vue.esm-bundler.js`.

Nếu bạn cần một lựa chọn nhẹ hơn cho cách dùng không có build step, hãy xem [petite-vue](https://github.com/vuejs/petite-vue).

## Hỗ Trợ IDE {#ide-support}

- Thiết lập IDE được khuyến nghị là [VS Code](https://code.visualstudio.com/) + [Vue - Official extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (trước đây là Volar). Extension này cung cấp tô sáng cú pháp, hỗ trợ TypeScript và intellisense cho biểu thức template cũng như props của component.

  :::tip
  Vue - Official thay thế [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), extension VS Code chính thức trước đây của chúng tôi cho Vue 2. Nếu hiện tại bạn đang cài Vetur, hãy nhớ tắt nó trong các dự án Vue 3.
  :::

- [WebStorm](https://www.jetbrains.com/webstorm/) cũng có hỗ trợ tích hợp rất tốt cho Vue SFC.

- Các IDE khác hỗ trợ [Language Service Protocol](https://microsoft.github.io/language-server-protocol/) (LSP) cũng có thể tận dụng chức năng cốt lõi của Volar thông qua LSP:

  - Hỗ trợ Sublime Text qua [LSP-Volar](https://github.com/sublimelsp/LSP-volar).

  - Hỗ trợ vim / Neovim qua [coc-volar](https://github.com/yaegassy/coc-volar).

  - Hỗ trợ emacs qua [lsp-mode](https://emacs-lsp.github.io/lsp-mode/page/lsp-volar/)

## Devtools Trên Trình Duyệt {#browser-devtools}

Extension Vue browser devtools cho phép bạn khám phá cây component của ứng dụng Vue, kiểm tra state của từng component, theo dõi sự kiện quản lý state và đo hiệu năng.

![devtools screenshot](./images/devtools.png)

- [Tài liệu](https://devtools.vuejs.org/)
- [Chrome Extension](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
- [Vite Plugin](https://devtools.vuejs.org/guide/vite-plugin)
- [Ứng dụng Electron độc lập](https://devtools.vuejs.org/guide/standalone)

## TypeScript {#typescript}

Bài viết chính: [Dùng Vue Với TypeScript](/guide/typescript/overview).

- [Vue - Official extension](https://github.com/vuejs/language-tools) cung cấp kiểm tra kiểu cho SFC dùng block `<script lang="ts">`, bao gồm cả biểu thức template và xác thực props xuyên component.

- Dùng [`vue-tsc`](https://github.com/vuejs/language-tools/tree/master/packages/tsc) để thực hiện cùng kiểu kiểm tra đó từ dòng lệnh, hoặc để sinh file `d.ts` cho SFC.

## Kiểm Thử {#testing}

Bài viết chính: [Hướng Dẫn Kiểm Thử](/guide/scaling-up/testing).

- [Cypress](https://www.cypress.io/) được khuyến nghị cho kiểm thử E2E. Nó cũng có thể dùng để kiểm thử component cho Vue SFC thông qua [Cypress Component Test Runner](https://docs.cypress.io/guides/component-testing/introduction).

- [Vitest](https://vitest.dev/) là test runner được tạo bởi các thành viên của đội Vue / Vite, tập trung vào tốc độ. Nó được thiết kế riêng cho ứng dụng dựa trên Vite để đem lại vòng phản hồi tức thì tương tự cho unit test / component test.

- [Jest](https://jestjs.io/) vẫn có thể dùng với Vite thông qua [vite-jest](https://github.com/sodatea/vite-jest). Tuy nhiên, điều này chỉ thật sự được khuyến nghị nếu bạn đã có sẵn test suite dựa trên Jest cần chuyển sang thiết lập dựa trên Vite, vì Vitest cung cấp chức năng tương tự với khả năng tích hợp hiệu quả hơn nhiều.

## Linting {#linting}

Đội ngũ Vue duy trì [eslint-plugin-vue](https://github.com/vuejs/eslint-plugin-vue), một plugin [ESLint](https://eslint.org/) hỗ trợ các quy tắc linting dành riêng cho SFC.

Người dùng trước đây dùng Vue CLI có thể quen với việc cấu hình linter thông qua webpack loader. Nhưng với thiết lập build dựa trên Vite, khuyến nghị chung của chúng tôi là:

1. `npm install -D eslint eslint-plugin-vue`, sau đó làm theo [hướng dẫn cấu hình](https://eslint.vuejs.org/user-guide/#usage) của `eslint-plugin-vue`.

2. Thiết lập extension ESLint trong IDE, ví dụ [ESLint for VS Code](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint), để nhận phản hồi từ linter ngay trong trình soạn thảo khi phát triển. Điều này cũng tránh chi phí linting không cần thiết khi khởi động dev server.

3. Chạy ESLint như một phần của lệnh build production để bạn nhận được toàn bộ phản hồi từ linter trước khi phát hành.

4. (Tùy chọn) Thiết lập các công cụ như [lint-staged](https://github.com/okonet/lint-staged) để tự động lint những file đã chỉnh sửa tại thời điểm git commit.

## Định Dạng Mã {#formatting}

- Extension [Vue - Official](https://github.com/vuejs/language-tools) cho VS Code hỗ trợ định dạng Vue SFC ngay khi cài.

- Ngoài ra, [Prettier](https://prettier.io/) cũng hỗ trợ định dạng Vue SFC sẵn có.

## Tích Hợp Với SFC Custom Block {#sfc-custom-block-integrations}

Custom block được biên dịch thành import tới cùng file Vue nhưng với query request khác. Build tool nền bên dưới sẽ chịu trách nhiệm xử lý những request import này.

- Nếu dùng Vite, bạn nên dùng custom Vite plugin để biến đổi các custom block khớp thành JavaScript có thể thực thi. [Ví dụ](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#example-for-transforming-custom-blocks)

- Nếu dùng Vue CLI hoặc webpack thuần, bạn nên cấu hình webpack loader để biến đổi các block phù hợp. [Ví dụ](https://vue-loader.vuejs.org/guide/custom-blocks.html)

## Các Package Ở Mức Thấp Hơn {#lower-level-packages}

### `@vue/compiler-sfc` {#vue-compiler-sfc}

- [Tài liệu](https://github.com/vuejs/core/tree/main/packages/compiler-sfc)

Package này là một phần của monorepo Vue core và luôn được phát hành cùng phiên bản với package `vue` chính. Nó được bao gồm như dependency của package `vue` chính và được proxy qua `vue/compiler-sfc`, nên bạn không cần cài riêng.

Bản thân package này cung cấp các utility ở mức thấp để xử lý Vue SFC và chỉ dành cho tác giả công cụ cần hỗ trợ Vue SFC trong công cụ tùy chỉnh của họ.

:::tip
Luôn ưu tiên dùng package này qua deep import `vue/compiler-sfc` vì cách này bảo đảm phiên bản của nó luôn đồng bộ với Vue runtime.
:::

### `@vitejs/plugin-vue` {#vitejs-plugin-vue}

- [Tài liệu](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue)

Plugin chính thức cung cấp hỗ trợ Vue SFC trong Vite.

### `vue-loader` {#vue-loader}

- [Tài liệu](https://vue-loader.vuejs.org/)

Loader chính thức cung cấp hỗ trợ Vue SFC trong webpack. Nếu bạn đang dùng Vue CLI, hãy xem thêm [tài liệu về cách chỉnh option `vue-loader` trong Vue CLI](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader).

## Các Playground Trực Tuyến Khác {#other-online-playgrounds}

- [VueUse Playground](https://play.vueuse.org)
- [Vue + Vite on Repl.it](https://replit.com/@templates/VueJS-with-Vite)
- [Vue on CodeSandbox](https://codesandbox.io/p/devbox/github/codesandbox/sandbox-templates/tree/main/vue-vite)
- [Vue on Codepen](https://codepen.io/pen/editor/vue)
- [Vue on WebComponents.dev](https://webcomponents.dev/create/cevue)

<!-- TODO ## Backend Framework Integrations -->
