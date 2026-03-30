# Thuật ngữ {#glossary}

Trang này nhằm giải thích ngắn gọn ý nghĩa của các thuật ngữ kỹ thuật thường gặp khi nói về Vue. Nội dung ở đây mang tính *mô tả* cách các thuật ngữ thường được dùng trong thực tế, chứ không phải một đặc tả *quy định* cách chúng bắt buộc phải được dùng. Tùy ngữ cảnh xung quanh, một số thuật ngữ có thể mang sắc thái hoặc ý nghĩa hơi khác nhau.

[[TOC]]

## async component {#async-component}

*Async component* là một lớp bọc quanh một component khác, cho phép component được bọc có thể được tải theo kiểu lazy. Cách này thường được dùng để giảm kích thước các tệp `.js` sau khi build, bằng cách tách chúng thành những chunk nhỏ hơn và chỉ tải khi thực sự cần.

Vue Router có một tính năng tương tự để [lazy load route component](https://router.vuejs.org/guide/advanced/lazy-loading.html), nhưng cơ chế đó không dùng tính năng async component của Vue.

Xem thêm chi tiết:
- [Guide - Async Components](/guide/components/async.html)

## compiler macro {#compiler-macro}

*Compiler macro* là đoạn mã đặc biệt được compiler xử lý rồi chuyển thành thứ khác. Về bản chất, đó là một dạng thay thế chuỗi thông minh.

Compiler của [SFC](#single-file-component) trong Vue hỗ trợ nhiều macro như `defineProps()`, `defineEmits()` và `defineExpose()`. Những macro này được cố ý thiết kế để trông giống hàm JavaScript thông thường, nhờ đó chúng có thể tận dụng cùng parser và bộ công cụ suy luận kiểu cho JavaScript / TypeScript. Tuy vậy, chúng không phải là những hàm thực sự chạy trong trình duyệt. Đây là các chuỗi đặc biệt mà compiler nhận diện rồi thay thế bằng đoạn JavaScript thật sự sẽ được chạy.

Macro có những giới hạn sử dụng mà JavaScript thông thường không có. Ví dụ, bạn có thể nghĩ rằng `const dp = defineProps` sẽ cho phép tạo bí danh cho `defineProps`, nhưng thực tế nó sẽ gây lỗi. Những giá trị được truyền vào `defineProps()` cũng bị giới hạn, vì các "đối số" đó phải được compiler xử lý chứ không phải xử lý lúc runtime.

Xem thêm chi tiết:
- [`<script setup>` - `defineProps()` & `defineEmits()`](/api/sfc-script-setup.html#defineprops-defineemits)
- [`<script setup>` - `defineExpose()`](/api/sfc-script-setup.html#defineexpose)

## component {#component}

Thuật ngữ *component* không phải chỉ riêng Vue mới có. Nó phổ biến trong nhiều UI framework và dùng để chỉ một phần của giao diện, chẳng hạn như một nút bấm hoặc checkbox. Các component cũng có thể được kết hợp lại để tạo thành những component lớn hơn.

Component là cơ chế cốt lõi mà Vue cung cấp để chia giao diện thành các phần nhỏ hơn, vừa giúp dễ bảo trì hơn, vừa cho phép tái sử dụng mã.

Một Vue component là một object. Mọi thuộc tính đều là tùy chọn, nhưng component phải có template hoặc render function thì mới có thể render được. Ví dụ, object sau là một component hợp lệ:

```js
const HelloWorldComponent = {
  render() {
    return 'Hello world!'
  }
}
```

Trong thực tế, phần lớn ứng dụng Vue được viết bằng [Single-File Components](#single-file-component) (tệp `.vue`). Dù thoạt nhìn các component này có thể không giống object, compiler SFC vẫn sẽ chuyển chúng thành một object và dùng object đó làm default export cho tệp. Nếu nhìn từ bên ngoài, một tệp `.vue` chỉ đơn giản là một ES module export ra một component object.

Các thuộc tính của component object thường được gọi là *option*. Đây cũng là lý do [Options API](#options-api) có tên như vậy.

Các option của một component định nghĩa cách các instance của component đó được tạo ra. Xét về mặt khái niệm, component khá giống class, dù Vue không dùng JavaScript class thật để định nghĩa chúng.

Thuật ngữ component đôi khi cũng được dùng theo nghĩa rộng hơn để chỉ component instance.

Xem thêm chi tiết:
- [Guide - Component Basics](/guide/essentials/component-basics.html)

Từ "component" cũng xuất hiện trong nhiều thuật ngữ khác:
- [async component](#async-component)
- [dynamic component](#dynamic-component)
- [functional component](#functional-component)
- [Web Component](#web-component)

## composable {#composable}

Thuật ngữ *composable* mô tả một mẫu sử dụng rất phổ biến trong Vue. Đây không phải là một tính năng riêng biệt của Vue, mà chỉ là một cách dùng [Composition API](#composition-api) của framework.

* Composable là một hàm.
* Composable được dùng để đóng gói và tái sử dụng logic có state.
* Tên hàm thường bắt đầu bằng `use` để những lập trình viên khác biết đây là composable.
* Hàm này thường được kỳ vọng sẽ được gọi trong quá trình thực thi đồng bộ của hàm `setup()` của component, hoặc tương đương là trong lúc chạy khối `<script setup>`. Điều này gắn lời gọi composable với ngữ cảnh component hiện tại, ví dụ thông qua `provide()`, `inject()` hoặc `onMounted()`.
* Composable thường trả về một object thuần, không phải reactive object. Object này thường chứa ref và function, và thường sẽ được destructure ở nơi sử dụng.

Giống như nhiều pattern khác, đôi khi sẽ có ý kiến khác nhau về việc một đoạn code cụ thể có được xem là composable hay không. Không phải mọi hàm tiện ích JavaScript đều là composable. Nếu một hàm không dùng Composition API thì nhiều khả năng nó không phải composable. Nếu nó không được thiết kế để gọi trong quá trình thực thi đồng bộ của `setup()` thì cũng có thể nó không phải composable. Composable được dùng riêng cho việc đóng gói logic có state, chứ không đơn thuần chỉ là một quy ước đặt tên hàm.

Xem [Guide - Composables](/guide/reusability/composables.html) để biết thêm chi tiết về cách viết composable.

## Composition API {#composition-api}

*Composition API* là một tập hợp các hàm được dùng để viết component và composable trong Vue.

Thuật ngữ này cũng được dùng để chỉ một trong hai phong cách chính để viết component, phong cách còn lại là [Options API](#options-api). Component viết bằng Composition API sẽ dùng `<script setup>` hoặc hàm `setup()` tường minh.

Xem [Composition API FAQ](/guide/extras/composition-api-faq) để biết thêm chi tiết.

## custom element {#custom-element}

*Custom element* là một tính năng thuộc tiêu chuẩn [Web Components](#web-component), được hỗ trợ trong các trình duyệt web hiện đại. Nó chỉ khả năng dùng một phần tử HTML tùy chỉnh trong markup HTML để chèn một Web Component vào đúng vị trí đó trên trang.

Vue có hỗ trợ tích hợp sẵn để render custom element và cho phép dùng chúng trực tiếp trong template của Vue component.

Không nên nhầm custom element với việc đặt một Vue component làm thẻ bên trong template của component Vue khác. Custom element được dùng để tạo Web Component, không phải để tạo Vue component.

Xem thêm chi tiết:
- [Guide - Vue and Web Components](/guide/extras/web-components.html)

## directive {#directive}

Thuật ngữ *directive* dùng để chỉ các thuộc tính template bắt đầu bằng tiền tố `v-`, hoặc cú pháp rút gọn tương đương của chúng.

Các directive dựng sẵn gồm có `v-if`, `v-for`, `v-bind`, `v-on` và `v-slot`.

Vue cũng hỗ trợ tạo custom directive, dù trên thực tế chúng thường chỉ được dùng như một "lối thoát" khi cần thao tác trực tiếp với DOM node. Nhìn chung, custom directive không thể dùng để tái tạo lại chức năng của các directive dựng sẵn.

Xem thêm chi tiết:
- [Guide - Template Syntax - Directives](/guide/essentials/template-syntax.html#directives)
- [Guide - Custom Directives](/guide/reusability/custom-directives.html)

## dynamic component {#dynamic-component}

Thuật ngữ *dynamic component* dùng để chỉ những trường hợp mà việc chọn component con nào sẽ được render cần được quyết định động. Thông thường, điều này được thực hiện bằng `<component :is="type">`.

Dynamic component không phải là một loại component đặc biệt. Bất kỳ component nào cũng có thể được dùng như dynamic component. Điều động ở đây là lựa chọn component, chứ không phải bản thân component.

Xem thêm chi tiết:
- [Guide - Components Basics - Dynamic Components](/guide/essentials/component-basics.html#dynamic-components)

## effect {#effect}

Xem [reactive effect](#reactive-effect) và [side effect](#side-effect).

## event {#event}

Việc dùng event để giao tiếp giữa các phần khác nhau của một chương trình là điều phổ biến trong nhiều lĩnh vực lập trình. Trong Vue, thuật ngữ này thường được dùng cho cả native event của phần tử HTML và event của Vue component. Directive `v-on` trong template được dùng để lắng nghe cả hai loại event này.

Xem thêm chi tiết:
- [Guide - Event Handling](/guide/essentials/event-handling.html)
- [Guide - Component Events](/guide/components/events.html)

## fragment {#fragment}

Thuật ngữ *fragment* dùng để chỉ một loại [VNode](#vnode) đặc biệt, được dùng làm node cha cho các VNode khác nhưng bản thân nó không render ra phần tử nào.

Tên gọi này xuất phát từ khái niệm tương tự là [`DocumentFragment`](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) trong DOM API gốc.

Fragment được dùng để hỗ trợ những component có nhiều root node. Dù các component như vậy có vẻ như có nhiều root, bên trong chúng vẫn dùng một fragment node làm root duy nhất, đóng vai trò node cha của các node "gốc".

Fragment cũng được template compiler dùng để bọc nhiều dynamic node, chẳng hạn các node được tạo bởi `v-for` hoặc `v-if`. Nhờ vậy, compiler có thể truyền thêm gợi ý cho thuật toán patch của [VDOM](#virtual-dom). Phần lớn cơ chế này được xử lý nội bộ, nhưng có một trường hợp bạn có thể gặp trực tiếp là dùng `key` trên thẻ `<template>` có `v-for`. Khi đó, `key` sẽ được thêm như một [prop](#prop) vào fragment VNode.

Hiện tại, fragment node được render ra DOM dưới dạng text node rỗng, dù đây chỉ là chi tiết triển khai. Bạn có thể bắt gặp các text node đó nếu dùng `$el` hoặc khi tự duyệt DOM bằng browser API tích hợp sẵn.

## functional component {#functional-component}

Định nghĩa component thường là một object chứa các option. Nếu bạn dùng `<script setup>` thì điều này có thể không rõ ràng, nhưng component được export từ tệp `.vue` vẫn là một object.

*Functional component* là một dạng component thay thế, được khai báo bằng một hàm. Hàm đó đóng vai trò [render function](#render-function) của component.

Functional component không có state riêng. Nó cũng không đi qua vòng đời component thông thường, nên không thể dùng lifecycle hook. Vì vậy chúng nhẹ hơn đôi chút so với component có state thông thường.

Xem thêm chi tiết:
- [Guide - Render Functions & JSX - Functional Components](/guide/extras/render-function.html#functional-components)

## hoisting {#hoisting}

Thuật ngữ *hoisting* dùng để mô tả việc chạy một đoạn mã trước khi tới vị trí của nó, tức là được đưa lên chạy trước các đoạn mã khác.

JavaScript dùng hoisting cho một số cấu trúc như `var`, `import` và khai báo hàm.

Trong ngữ cảnh Vue, compiler áp dụng *hoisting* để cải thiện hiệu năng. Khi biên dịch component, các giá trị tĩnh sẽ được đưa ra ngoài phạm vi của component. Những giá trị tĩnh này được gọi là đã được "hoist" vì chúng được tạo bên ngoài component.

## cache static {#cache-static}

Thuật ngữ *cache* dùng để chỉ việc lưu tạm dữ liệu được truy cập thường xuyên nhằm cải thiện hiệu năng.

Template compiler của Vue sẽ nhận diện các VNode tĩnh, lưu cache chúng trong lần render đầu tiên và tái sử dụng chính các VNode đó cho mọi lần re-render về sau.

Xem thêm chi tiết:
- [Guide - Rendering Mechanism - Cache Static](/guide/extras/rendering-mechanism.html#cache-static)

## in-DOM template {#in-dom-template}

Có nhiều cách để khai báo template cho component. Trong phần lớn trường hợp, template được cung cấp dưới dạng chuỗi.

Thuật ngữ *in-DOM template* dùng để chỉ tình huống template được cung cấp dưới dạng DOM node thay vì chuỗi. Sau đó Vue sẽ chuyển các DOM node đó thành chuỗi template bằng `innerHTML`.

Thông thường, in-DOM template bắt đầu là HTML markup được viết trực tiếp trong HTML của trang. Trình duyệt sẽ parse nó thành DOM node, rồi Vue dùng các node đó để đọc `innerHTML`.

Xem thêm chi tiết:
- [Guide - Creating an Application - In-DOM Root Component Template](/guide/essentials/application.html#in-dom-root-component-template)
- [Guide - Component Basics - in-DOM Template Parsing Caveats](/guide/essentials/component-basics.html#in-dom-template-parsing-caveats)
- [Options: Rendering - template](/api/options-rendering.html#template)

## inject {#inject}

Xem [provide / inject](#provide-inject).

## lifecycle hooks {#lifecycle-hooks}

Một Vue component instance sẽ trải qua một vòng đời. Ví dụ, nó được tạo ra, mount, cập nhật rồi unmount.

*Lifecycle hooks* là cách để lắng nghe những sự kiện vòng đời đó.

Với Options API, mỗi hook được cung cấp như một option riêng, ví dụ `mounted`. Với Composition API, chúng được biểu diễn bằng các hàm như `onMounted()`.

Xem thêm chi tiết:
- [Guide - Lifecycle Hooks](/guide/essentials/lifecycle.html)

## macro {#macro}

Xem [compiler macro](#compiler-macro).

## named slot {#named-slot}

Một component có thể có nhiều slot, được phân biệt bằng tên. Những slot khác với default slot được gọi là *named slot*.

Xem thêm chi tiết:
- [Guide - Slots - Named Slots](/guide/components/slots.html#named-slots)

## Options API {#options-api}

Vue component được định nghĩa bằng object. Các thuộc tính của những component object này được gọi là *option*.

Component có thể được viết theo hai phong cách. Một phong cách dùng [Composition API](#composition-api) cùng với `setup` (thông qua option `setup()` hoặc `<script setup>`). Phong cách còn lại hầu như không dùng trực tiếp Composition API, mà thay vào đó dùng nhiều component option khác nhau để đạt kết quả tương tự. Tập hợp các component option được dùng theo cách này được gọi là *Options API*.

Options API bao gồm các option như `data()`, `computed`, `methods` và `created()`.

Một số option như `props`, `emits` và `inheritAttrs` có thể được dùng khi viết component với cả hai API. Vì chúng là component option, ta có thể xem chúng thuộc Options API. Tuy nhiên, do các option này cũng thường được dùng cùng `setup()`, nên trong thực tế sẽ hữu ích hơn nếu xem chúng là phần dùng chung giữa hai phong cách viết component.

Chính hàm `setup()` cũng là một component option, nên về mặt lý thuyết nó *có thể* được mô tả là một phần của Options API. Tuy vậy, đó không phải cách người ta thường dùng thuật ngữ "Options API". Thay vào đó, `setup()` được xem là một phần của Composition API.

## plugin {#plugin}

Dù thuật ngữ *plugin* có thể được dùng trong rất nhiều ngữ cảnh, Vue có một khái niệm plugin riêng như một cách để bổ sung chức năng cho ứng dụng.

Plugin được thêm vào ứng dụng bằng cách gọi `app.use(plugin)`. Bản thân plugin có thể là một hàm hoặc một object có hàm `install`. Hàm đó sẽ nhận vào instance ứng dụng và sau đó có thể làm những gì nó cần làm.

Xem thêm chi tiết:
- [Guide - Plugins](/guide/reusability/plugins.html)

## prop {#prop}

Trong Vue, thuật ngữ *prop* thường có ba cách dùng phổ biến:

* Component props
* VNode props
* Slot props

*Component props* là điều mà đa số mọi người nghĩ tới khi nói về props. Chúng được component khai báo tường minh bằng `defineProps()` hoặc option `props`.

Thuật ngữ *VNode props* dùng để chỉ các thuộc tính của object được truyền làm đối số thứ hai cho `h()`. Chúng có thể bao gồm component props, nhưng cũng có thể bao gồm component event, DOM event, DOM attribute và DOM property. Thông thường bạn chỉ gặp VNode props khi làm việc với render function để thao tác trực tiếp với VNode.

*Slot props* là các thuộc tính được truyền vào scoped slot.

Trong mọi trường hợp, props là các thuộc tính được truyền từ nơi khác vào.

Dù từ props bắt nguồn từ *properties*, nhưng trong ngữ cảnh Vue, props mang ý nghĩa cụ thể hơn nhiều. Bạn nên tránh dùng nó như một cách viết tắt thông thường của properties.

Xem thêm chi tiết:
- [Guide - Props](/guide/components/props.html)
- [Guide - Render Functions & JSX](/guide/extras/render-function.html)
- [Guide - Slots - Scoped Slots](/guide/components/slots.html#scoped-slots)

## provide / inject {#provide-inject}

`provide` và `inject` là một hình thức giao tiếp giữa các component.

Khi một component *provide* một giá trị, mọi component con cháu của nó đều có thể chọn lấy giá trị đó bằng `inject`. Khác với props, component cung cấp giá trị không biết chính xác component nào sẽ nhận giá trị đó.

`provide` và `inject` đôi khi được dùng để tránh *prop drilling*. Chúng cũng có thể được dùng như một cách ngầm định để component giao tiếp với nội dung trong slot của nó.

`provide` cũng có thể được dùng ở cấp ứng dụng, giúp một giá trị có sẵn cho mọi component trong ứng dụng đó.

Xem thêm chi tiết:
- [Guide - provide / inject](/guide/components/provide-inject.html)

## reactive effect {#reactive-effect}

*Reactive effect* là một phần trong hệ thống tính phản ứng của Vue. Nó chỉ quá trình theo dõi dependency của một hàm và chạy lại hàm đó khi giá trị của các dependency thay đổi.

`watchEffect()` là cách trực tiếp nhất để tạo effect. Nhiều phần khác của Vue cũng dùng effect nội bộ, chẳng hạn như cập nhật render của component, `computed()` và `watch()`.

Vue chỉ có thể theo dõi reactive dependency bên trong một reactive effect. Nếu giá trị của một thuộc tính được đọc bên ngoài reactive effect thì nó sẽ "mất" tính phản ứng, theo nghĩa Vue sẽ không biết cần làm gì nếu thuộc tính đó thay đổi sau đó.

Thuật ngữ này bắt nguồn từ "side effect". Việc gọi effect function chính là một side effect của việc giá trị thuộc tính thay đổi.

Xem thêm chi tiết:
- [Guide - Reactivity in Depth](/guide/extras/reactivity-in-depth.html)

## reactivity {#reactivity}

Nói chung, *reactivity* là khả năng tự động thực hiện hành động để phản hồi lại việc dữ liệu thay đổi. Ví dụ: cập nhật DOM hoặc gửi network request khi một giá trị dữ liệu thay đổi.

Trong ngữ cảnh Vue, reactivity dùng để chỉ một nhóm tính năng kết hợp với nhau thành một *hệ thống tính phản ứng*, được phơi bày qua [Reactivity API](#reactivity-api).

Có nhiều cách khác nhau để triển khai một hệ thống tính phản ứng. Ví dụ, có thể dùng static analysis trên code để xác định dependency. Tuy nhiên, Vue không dùng cách tiếp cận đó.

Thay vào đó, hệ thống tính phản ứng của Vue theo dõi việc truy cập thuộc tính ở runtime. Nó làm điều này bằng cả lớp bọc Proxy lẫn các hàm [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) / [setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description) cho thuộc tính.

Xem thêm chi tiết:
- [Guide - Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals.html)
- [Guide - Reactivity in Depth](/guide/extras/reactivity-in-depth.html)

## Reactivity API {#reactivity-api}

*Reactivity API* là tập hợp các hàm cốt lõi của Vue liên quan tới [reactivity](#reactivity). Chúng có thể được dùng độc lập với component. Nó bao gồm các hàm như `ref()`, `reactive()`, `computed()`, `watch()` và `watchEffect()`.

Reactivity API là một tập con của Composition API.

Xem thêm chi tiết:
- [Reactivity API: Core](/api/reactivity-core.html)
- [Reactivity API: Utilities](/api/reactivity-utilities.html)
- [Reactivity API: Advanced](/api/reactivity-advanced.html)

## ref {#ref}

> Mục này nói về `ref` trong hệ thống phản ứng. Với thuộc tính `ref` dùng trong template, hãy xem [template ref](#template-ref).

`ref` là một phần của hệ thống tính phản ứng trong Vue. Nó là một object có một thuộc tính phản ứng duy nhất, tên là `value`.

Có nhiều loại ref khác nhau. Ví dụ, bạn có thể tạo ref bằng `ref()`, `shallowRef()`, `computed()` và `customRef()`. Hàm `isRef()` có thể dùng để kiểm tra một object có phải ref hay không, còn `isReadonly()` có thể dùng để kiểm tra ref đó có cho phép gán lại trực tiếp giá trị của nó hay không.

Xem thêm chi tiết:
- [Guide - Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals.html)
- [Reactivity API: Core](/api/reactivity-core.html)
- [Reactivity API: Utilities](/api/reactivity-utilities.html)
- [Reactivity API: Advanced](/api/reactivity-advanced.html)

## render function {#render-function}

*Render function* là phần của component tạo ra các VNode được dùng trong quá trình render. Template sẽ được biên dịch thành render function.

Xem thêm chi tiết:
- [Guide - Render Functions & JSX](/guide/extras/render-function.html)

## scheduler {#scheduler}

*Scheduler* là phần bên trong Vue kiểm soát thời điểm chạy của các [reactive effect](#reactive-effect).

Khi reactive state thay đổi, Vue không kích hoạt cập nhật render ngay lập tức. Thay vào đó, nó gom các thay đổi lại bằng một hàng đợi. Điều này bảo đảm component chỉ re-render một lần, ngay cả khi dữ liệu gốc thay đổi nhiều lần.

[Watcher](/guide/essentials/watchers.html) cũng được gom lô bằng hàng đợi scheduler. Watcher có `flush: 'pre'` (mặc định) sẽ chạy trước khi component render, còn watcher có `flush: 'post'` sẽ chạy sau khi component render.

Các job trong scheduler cũng được dùng để thực hiện nhiều tác vụ nội bộ khác, chẳng hạn kích hoạt một số [lifecycle hook](#lifecycle-hooks) và cập nhật [template ref](#template-ref).

## scoped slot {#scoped-slot}

Thuật ngữ *scoped slot* dùng để chỉ một [slot](#slot) nhận [prop](#prop).

Trong lịch sử, Vue phân biệt scoped slot và non-scoped slot rõ ràng hơn nhiều. Ở một mức độ nào đó, chúng có thể được xem là hai tính năng riêng, chỉ là cùng dùng chung một cú pháp template.

Trong Vue 3, API của slot đã được đơn giản hóa để mọi slot đều hoạt động như scoped slot. Tuy vậy, trường hợp sử dụng của scoped slot và non-scoped slot thường vẫn khác nhau, nên thuật ngữ này vẫn hữu ích để chỉ những slot có prop.

Các prop truyền vào slot chỉ có thể dùng trong một vùng nhất định của template cha, tức vùng chịu trách nhiệm định nghĩa nội dung của slot. Vùng đó trong template hoạt động như một phạm vi biến cho các prop, vì thế mới có tên là "scoped slot".

Xem thêm chi tiết:
- [Guide - Slots - Scoped Slots](/guide/components/slots.html#scoped-slots)

## SFC {#sfc}

Xem [Single-File Component](#single-file-component).

## side effect {#side-effect}

Thuật ngữ *side effect* không phải là khái niệm riêng của Vue. Nó dùng để mô tả các thao tác hoặc hàm làm điều gì đó vượt ra ngoài phạm vi cục bộ của chính nó.

Ví dụ, trong ngữ cảnh gán thuộc tính như `user.name = null`, ta kỳ vọng giá trị của `user.name` sẽ thay đổi. Nếu ngoài chuyện đó nó còn làm thêm việc khác, chẳng hạn kích hoạt hệ thống tính phản ứng của Vue, thì đó sẽ được mô tả là một side effect. Đây cũng là nguồn gốc của thuật ngữ [reactive effect](#reactive-effect) trong Vue.

Khi một hàm được mô tả là có side effect, điều đó có nghĩa là hàm thực hiện một dạng hành động nào đó có thể quan sát được từ bên ngoài hàm, ngoài việc chỉ trả về một giá trị. Ví dụ, nó có thể cập nhật state hoặc kích hoạt một network request.

Thuật ngữ này thường xuất hiện khi nói về render hoặc computed property. Thực hành tốt nhất là quá trình render không có side effect. Tương tự, hàm getter của computed property cũng không nên có side effect.

## Single-File Component {#single-file-component}

Thuật ngữ *Single-File Component*, hay SFC, dùng để chỉ định dạng tệp `.vue` thường được dùng cho Vue component.

Xem thêm:
- [Guide - Single-File Components](/guide/scaling-up/sfc.html)
- [SFC Syntax Specification](/api/sfc-spec.html)

## slot {#slot}

Slot được dùng để truyền nội dung vào component con. Trong khi props dùng để truyền giá trị dữ liệu, slot dùng để truyền nội dung phong phú hơn gồm phần tử HTML và các Vue component khác.

Xem thêm chi tiết:
- [Guide - Slots](/guide/components/slots.html)

## template ref {#template-ref}

Thuật ngữ *template ref* dùng để chỉ việc đặt thuộc tính `ref` trên một thẻ trong template. Sau khi component render xong, thuộc tính này sẽ được dùng để gán một thuộc tính tương ứng bằng phần tử HTML hoặc component instance tương ứng với thẻ đó trong template.

Nếu bạn dùng Options API thì các ref sẽ được phơi bày thông qua các thuộc tính của object `$refs`.

Với Composition API, template ref sẽ điền giá trị vào một [ref](#ref) phản ứng có cùng tên.

Không nên nhầm template ref với các reactive ref trong hệ thống tính phản ứng của Vue.

Xem thêm chi tiết:
- [Guide - Template Refs](/guide/essentials/template-refs.html)

## VDOM {#vdom}

Xem [virtual DOM](#virtual-dom).

## virtual DOM {#virtual-dom}

Thuật ngữ *virtual DOM* (VDOM) không phải là khái niệm riêng của Vue. Đây là một cách tiếp cận phổ biến được nhiều web framework dùng để quản lý việc cập nhật giao diện.

Trình duyệt dùng một cây node để biểu diễn trạng thái hiện tại của trang. Cây đó, cùng các JavaScript API dùng để tương tác với nó, được gọi là *document object model*, hay *DOM*.

Thao tác trực tiếp với DOM là một nút thắt lớn về hiệu năng. Virtual DOM đưa ra một chiến lược để xử lý vấn đề đó.

Thay vì tạo DOM node trực tiếp, Vue component sẽ tạo ra một bản mô tả về những DOM node mà nó muốn có. Các bản mô tả này là những object JavaScript thuần, được gọi là VNode (virtual DOM node). Việc tạo VNode tương đối rẻ.

Mỗi khi component re-render, cây VNode mới sẽ được so sánh với cây VNode trước đó, và mọi khác biệt sẽ được áp dụng lên DOM thật. Nếu không có gì thay đổi thì DOM sẽ không cần bị động tới.

Vue dùng một cách tiếp cận lai mà chúng tôi gọi là [Compiler-Informed Virtual DOM](/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom). Template compiler của Vue có thể áp dụng tối ưu hiệu năng dựa trên việc phân tích tĩnh template. Thay vì so sánh toàn bộ cây VNode cũ và mới của component ở runtime, Vue có thể dùng thông tin do compiler trích xuất để thu hẹp việc so sánh xuống đúng những phần của cây thực sự có thể thay đổi.

Xem thêm chi tiết:
- [Guide - Rendering Mechanism](/guide/extras/rendering-mechanism.html)
- [Guide - Render Functions & JSX](/guide/extras/render-function.html)

## VNode {#vnode}

*VNode* là một *virtual DOM node*. Bạn có thể tạo nó bằng hàm [`h()`](/api/render-function.html#h).

Xem [virtual DOM](#virtual-dom) để biết thêm chi tiết.

## Web Component {#web-component}

Tiêu chuẩn *Web Components* là một tập hợp các tính năng được triển khai trong các trình duyệt web hiện đại.

Vue component không phải là Web Component, nhưng `defineCustomElement()` có thể được dùng để tạo một [custom element](#custom-element) từ Vue component. Vue cũng hỗ trợ dùng custom element bên trong Vue component.

Xem thêm chi tiết:
- [Guide - Vue and Web Components](/guide/extras/web-components.html)
