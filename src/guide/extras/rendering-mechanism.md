---
outline: deep
---

# Cơ Chế Render {#rendering-mechanism}

Làm thế nào Vue lấy một template rồi biến nó thành các DOM node thực sự? Làm thế nào Vue cập nhật các DOM node đó một cách hiệu quả? Ở đây, ta sẽ cố gắng làm sáng tỏ những câu hỏi này bằng cách đi sâu vào cơ chế render nội bộ của Vue.

## Virtual DOM {#virtual-dom}

Có lẽ bạn đã từng nghe đến khái niệm "virtual DOM", nền tảng mà hệ thống render của Vue dựa vào.

Virtual DOM (VDOM) là một khái niệm lập trình trong đó một biểu diễn UI lý tưởng, hay "ảo", được giữ trong bộ nhớ rồi đồng bộ với DOM "thật". Khái niệm này được [React](https://react.dev/) khai phá đầu tiên, và sau đó được nhiều framework khác áp dụng với các cách cài đặt khác nhau, trong đó có Vue.

Virtual DOM là một pattern nhiều hơn là một công nghệ cụ thể, nên không có một cách cài đặt chuẩn duy nhất nào. Ta có thể minh họa ý tưởng này bằng một ví dụ đơn giản:

```js
const vnode = {
  type: 'div',
  props: {
    id: 'hello'
  },
  children: [
    /* thêm vnode con */
  ]
}
```

Ở đây, `vnode` là một object JavaScript thuần (một "virtual node") đại diện cho phần tử `<div>`. Nó chứa toàn bộ thông tin cần thiết để tạo ra phần tử thật. Nó cũng chứa thêm các vnode con, khiến nó trở thành gốc của một cây virtual DOM.

Một renderer runtime có thể duyệt cây virtual DOM rồi dựng nên cây DOM thật từ đó. Quá trình này được gọi là **mount**.

Nếu ta có hai bản sao của cây virtual DOM, renderer cũng có thể duyệt và so sánh hai cây, tìm ra sự khác biệt rồi áp dụng những thay đổi đó lên DOM thật. Quá trình này được gọi là **patch**, còn được biết tới với tên "diffing" hoặc "reconciliation".

Lợi ích chính của virtual DOM là nó cho phép developer tạo, kiểm tra và kết hợp cấu trúc UI mong muốn theo cách khai báo bằng mã lệnh, trong khi việc thao tác DOM trực tiếp được giao lại cho renderer.

## Pipeline Render {#render-pipeline}

Ở mức khái quát, đây là điều xảy ra khi một component Vue được mount:

1. **Compile**: Template Vue được biên dịch thành **render function**, tức các hàm trả về cây virtual DOM. Bước này có thể được thực hiện trước thông qua build step, hoặc thực hiện ngay lúc chạy bằng runtime compiler.

2. **Mount**: Renderer runtime gọi các render function, duyệt cây virtual DOM trả về, rồi tạo ra các DOM node thật từ đó. Bước này được thực hiện dưới dạng một [reactive effect](./reactivity-in-depth), vì vậy nó sẽ theo dõi mọi dependency phản ứng đã được dùng.

3. **Patch**: Khi một dependency được dùng trong lúc mount thay đổi, effect sẽ chạy lại. Lần này, một cây Virtual DOM mới đã cập nhật sẽ được tạo ra. Renderer runtime duyệt cây mới, so sánh nó với cây cũ và áp dụng những cập nhật cần thiết lên DOM thật.

![render pipeline](./images/render-pipeline.png)

<!-- https://www.figma.com/file/elViLsnxGJ9lsQVsuhwqxM/Rendering-Mechanism -->

## Template So Với Render Function {#templates-vs-render-functions}

Template Vue được biên dịch thành render function tạo virtual DOM. Vue cũng cung cấp API cho phép ta bỏ qua bước biên dịch template và tự viết render function trực tiếp. Render function linh hoạt hơn template khi phải xử lý logic rất động, vì bạn có thể thao tác với vnode bằng toàn bộ sức mạnh của JavaScript.

Vậy tại sao Vue vẫn khuyến nghị dùng template theo mặc định? Có một số lý do:

1. Template gần với HTML thực hơn. Điều này giúp tái sử dụng snippet HTML hiện có dễ hơn, áp dụng best practice về khả năng tiếp cận, style bằng CSS, đồng thời cũng giúp designer dễ hiểu và dễ chỉnh sửa hơn.

2. Template dễ được phân tích tĩnh hơn nhờ cú pháp mang tính xác định cao hơn. Điều này cho phép trình biên dịch template của Vue áp dụng nhiều tối ưu ở thời điểm biên dịch để cải thiện hiệu năng của virtual DOM, điều mà ta sẽ bàn ngay dưới đây.

Trên thực tế, template là đủ cho hầu hết trường hợp trong ứng dụng. Render function thường chỉ được dùng trong các component có thể tái sử dụng cần xử lý logic render rất động. Cách dùng render function được trình bày chi tiết hơn trong [Render Functions & JSX](./render-function).

## Virtual DOM Được Trình Biên Dịch Hỗ Trợ {#compiler-informed-virtual-dom}

Cách cài đặt virtual DOM trong React và đa số hệ thống virtual DOM khác đều thuần runtime: thuật toán reconciliation không thể đưa ra giả định nào về cây virtual DOM đi vào, nên nó phải duyệt toàn bộ cây và diff props của từng vnode để bảo đảm tính đúng đắn. Ngoài ra, kể cả khi một phần của cây không bao giờ thay đổi, mỗi lần render lại vẫn luôn tạo ra vnode mới cho phần đó, gây áp lực bộ nhớ không cần thiết. Đây là một trong những điểm bị phê bình nhiều nhất của virtual DOM: quy trình reconciliation tương đối kiểu "quét thẳng tay" hy sinh hiệu quả để đổi lấy tính khai báo và tính đúng đắn.

Nhưng không nhất thiết phải như vậy. Trong Vue, framework kiểm soát cả trình biên dịch lẫn runtime. Điều này cho phép ta triển khai nhiều tối ưu ở thời điểm biên dịch mà chỉ renderer gắn kết chặt chẽ mới có thể tận dụng được. Trình biên dịch có thể phân tích tĩnh template và để lại gợi ý trong mã được sinh ra để runtime có thể đi đường tắt bất cứ khi nào có thể. Đồng thời, ta vẫn giữ khả năng cho người dùng hạ xuống tầng render function để kiểm soát trực tiếp hơn trong những edge case. Chúng tôi gọi cách tiếp cận lai này là **Compiler-Informed Virtual DOM**.

Bên dưới, ta sẽ bàn về một vài tối ưu chính mà trình biên dịch template của Vue thực hiện để cải thiện hiệu năng runtime của virtual DOM.

### Cache Phần Tĩnh {#cache-static}

Rất thường xuyên sẽ có những phần trong template không chứa bất kỳ ràng buộc động nào:

```vue-html{2-3}
<div>
  <div>foo</div> <!-- được cache -->
  <div>bar</div> <!-- được cache -->
  <div>{{ dynamic }}</div>
</div>
```

[Kiểm tra trong Template Explorer](https://template-explorer.vuejs.org/#eyJzcmMiOiI8ZGl2PlxuICA8ZGl2PmZvbzwvZGl2PiA8IS0tIGNhY2hlZCAtLT5cbiAgPGRpdj5iYXI8L2Rpdj4gPCEtLSBjYWNoZWQgLS0+XG4gIDxkaXY+e3sgZHluYW1pYyB9fTwvZGl2PlxuPC9kaXY+XG4iLCJvcHRpb25zIjp7ImhvaXN0U3RhdGljIjp0cnVlfX0=)

Hai `div` chứa `foo` và `bar` là phần tĩnh. Việc tạo lại vnode và diff chúng ở mỗi lần render lại là không cần thiết. Renderer tạo các vnode này ở lần render đầu tiên, cache chúng lại và tái sử dụng cùng các vnode đó cho mọi lần render sau. Renderer cũng có thể bỏ qua hoàn toàn việc diff chúng khi nhận ra vnode cũ và vnode mới chính là cùng một đối tượng.

Ngoài ra, khi có đủ nhiều phần tử tĩnh đứng liền nhau, chúng sẽ được gom thành một "static vnode" duy nhất chứa chuỗi HTML thô của toàn bộ các node đó ([Ví dụ](https://template-explorer.vuejs.org/#eyJzcmMiOiI8ZGl2PlxuICA8ZGl2IGNsYXNzPVwiZm9vXCI+Zm9vPC9kaXY+XG4gIDxkaXYgY2xhc3M9XCJmb29cIj5mb288L2Rpdj5cbiAgPGRpdiBjbGFzcz1cImZvb1wiPmZvbzwvZGl2PlxuICA8ZGl2IGNsYXNzPVwiZm9vXCI+Zm9vPC9kaXY+XG4gIDxkaXYgY2xhc3M9XCJmb29cIj5mb288L2Rpdj5cbiAgPGRpdiB7eyBkeW5hbWljIH19PC9kaXY+XG48L2Rpdj4iLCJzc3IiOmZhbHNlLCJvcHRpb25zIjp7ImhvaXN0U3RhdGljIjp0cnVlfX0=)). Những static vnode này được mount bằng cách gán trực tiếp `innerHTML`.

### Patch Flag {#patch-flags}

Với một phần tử đơn có ràng buộc động, ta cũng có thể suy ra khá nhiều thông tin từ lúc biên dịch:

```vue-html
<!-- chỉ bind class -->
<div :class="{ active }"></div>

<!-- chỉ bind id và value -->
<input :id="id" :value="value">

<!-- chỉ có text con -->
<div>{{ dynamic }}</div>
```

[Kiểm tra trong Template Explorer](https://template-explorer.vuejs.org/#eyJzcmMiOiI8ZGl2IDpjbGFzcz1cInsgYWN0aXZlIH1cIj48L2Rpdj5cblxuPGlucHV0IDppZD1cImlkXCIgOnZhbHVlPVwidmFsdWVcIj5cblxuPGRpdj57eyBkeW5hbWljIH19PC9kaXY+Iiwib3B0aW9ucyI6e319)

Khi sinh mã render function cho các phần tử này, Vue mã hóa trực tiếp kiểu cập nhật cần thiết của từng phần tử vào lệnh tạo vnode:

```js{3}
createElementVNode("div", {
  class: _normalizeClass({ active: _ctx.active })
}, null, 2 /* CLASS */)
```

Đối số cuối cùng, `2`, là một [patch flag](https://github.com/vuejs/core/blob/main/packages/shared/src/patchFlags.ts). Một phần tử có thể mang nhiều patch flag, và chúng sẽ được gộp thành một con số duy nhất. Renderer runtime sau đó có thể kiểm tra các flag bằng [bitwise operations](https://en.wikipedia.org/wiki/Bitwise_operation) để xác định xem có cần làm một công việc nào đó hay không:

```js
if (vnode.patchFlag & PatchFlags.CLASS /* 2 */) {
  // cập nhật class của phần tử
}
```

Bitwise check chạy cực nhanh. Với patch flag, Vue có thể làm đúng lượng công việc tối thiểu cần thiết khi cập nhật phần tử có ràng buộc động.

Vue cũng mã hóa cả kiểu con mà một vnode có. Ví dụ, một template có nhiều node gốc sẽ được biểu diễn bằng một fragment. Trong phần lớn trường hợp, ta biết chắc thứ tự của các node gốc này sẽ không bao giờ thay đổi, nên thông tin đó cũng có thể được cung cấp cho runtime dưới dạng patch flag:

```js{4}
export function render() {
  return (_openBlock(), _createElementBlock(_Fragment, null, [
    /* children */
  ], 64 /* STABLE_FRAGMENT */))
}
```

Nhờ đó runtime có thể bỏ qua hoàn toàn bước reconciliation thứ tự con cho fragment gốc.

### Làm Phẳng Cây {#tree-flattening}

Nếu nhìn lại đoạn mã sinh ra từ ví dụ trước, bạn sẽ thấy gốc của cây virtual DOM trả về được tạo bằng lời gọi đặc biệt `createElementBlock()`:

```js{2}
export function render() {
  return (_openBlock(), _createElementBlock(_Fragment, null, [
    /* children */
  ], 64 /* STABLE_FRAGMENT */))
}
```

Về mặt ý niệm, một "block" là phần của template có cấu trúc bên trong ổn định. Trong trường hợp này, toàn bộ template chỉ có một block vì nó không chứa directive cấu trúc như `v-if` và `v-for`.

Mỗi block sẽ theo dõi mọi node hậu duệ có patch flag, chứ không chỉ con trực tiếp. Ví dụ:

```vue-html{3,5}
<div> <!-- block gốc -->
  <div>...</div>         <!-- không được theo dõi -->
  <div :id="id"></div>   <!-- được theo dõi -->
  <div>                  <!-- không được theo dõi -->
    <div>{{ bar }}</div> <!-- được theo dõi -->
  </div>
</div>
```

Kết quả là một mảng đã làm phẳng chỉ chứa các node hậu duệ động:

```
div (gốc block)
- div có ràng buộc :id
- div có ràng buộc {{ bar }}
```

Khi component này cần render lại, nó chỉ cần duyệt cây đã làm phẳng thay vì duyệt toàn bộ cây. Đây được gọi là **Tree Flattening**, và nó giảm rất mạnh số lượng node cần phải duyệt trong quá trình reconciliation của virtual DOM. Mọi phần tĩnh của template về thực chất sẽ bị bỏ qua.

Directive `v-if` và `v-for` sẽ tạo ra block node mới:

```vue-html
<div> <!-- block gốc -->
  <div>
    <div v-if> <!-- if block -->
      ...
    </div>
  </div>
</div>
```

Một block con sẽ được theo dõi bên trong mảng hậu duệ động của block cha. Điều này giữ được cấu trúc ổn định cho block cha.

### Tác Động Lên SSR Hydration {#impact-on-ssr-hydration}

Cả patch flag lẫn tree flattening cũng cải thiện đáng kể hiệu năng [SSR Hydration](/guide/scaling-up/ssr#client-hydration) của Vue:

- Hydration cho phần tử đơn có thể đi theo nhánh nhanh dựa trên patch flag tương ứng của vnode.

- Chỉ block node và hậu duệ động của chúng mới cần được duyệt trong lúc hydration, nhờ đó về thực chất đạt được partial hydration ở cấp template.
