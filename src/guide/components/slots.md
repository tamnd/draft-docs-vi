# Slot {#slots}

> Trang này giả định rằng bạn đã đọc [Kiến thức cơ bản về Component](/guide/essentials/component-basics). Nếu chưa quen với component, hãy đọc trang đó trước.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-component-slots" title="Bài học miễn phí về slot trong Vue.js"/>

## Nội Dung Slot Và Vị Trí Hiển Thị Slot {#slot-content-and-outlet}

Ta đã biết component có thể nhận props, tức các giá trị JavaScript thuộc bất kỳ kiểu nào. Nhưng còn nội dung template thì sao? Trong một số trường hợp, ta muốn truyền một đoạn template vào component con, để component con tự render đoạn đó trong template của chính nó.

Ví dụ, ta có thể có component `<FancyButton>` hỗ trợ cách dùng như sau:

```vue-html{2}
<FancyButton>
  Click me! <!-- nội dung slot -->
</FancyButton>
```

Template của `<FancyButton>` trông như sau:

```vue-html{2}
<button class="fancy-btn">
  <slot></slot> <!-- vị trí hiển thị slot -->
</button>
```

Phần tử `<slot>` là một **slot outlet**, cho biết nơi **nội dung slot** do component cha cung cấp sẽ được render.

![slot diagram](./images/slots.png)

<!-- https://www.figma.com/file/LjKTYVL97Ck6TEmBbstavX/slot -->

Và DOM cuối cùng được render:

```html
<button class="fancy-btn">Click me!</button>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNpdUdlqAyEU/ZVbQ0kLMdNsXabTQFvoV8yLcRkkjopLSQj596oTwqRvnuM9y9UT+rR2/hs5qlHjqZM2gOch2m2rZW+NC/BDND1+xRCMBuFMD9N5NeKyeNrqphrUSZdA4L1VJPCEAJrRdCEAvpWke+g5NHcYg1cmADU6cB0A4zzThmYckqimupqiGfpXILe/zdwNhaki3n+0SOR5vAu6ReU++efUajtqYGJQ/FIg5w8Wt9FlOx+OKh/nV1c4ZVNqlHE1TIQQ7xnvCN13zkTNalBSc+Jw5wiTac2H1WLDeDeDyXrJVm9LWG7uE3hev3AhHge1cYwnO200L4QljEnd1bCxB1g82UNhe+I6qQs5kuGcE30NrxeaRudzOWtkemeXuHP5tLIKOv8BN+mw3w==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNpdUdtOwzAM/RUThAbSurIbl1ImARJf0ZesSapoqROlKdo07d9x0jF1SHmIT+xzcY7sw7nZTy9Zwcqu9tqFTYW6ddYH+OZYHz77ECyC8raFySwfYXFsUiFAhXKfBoRUvDcBjhGtLbGgxNAVcLziOlVIp8wvelQE2TrDg6QKoBx1JwDgy+h6B62E8ibLoDM2kAAGoocsiz1VKMfmCCrzCymbsn/GY95rze1grja8694rpmJ/tg1YsfRO/FE134wc2D4YeTYQ9QeKa+mUrgsHE6+zC+vfjoz1Bdwqpd5iveX1rvG2R1GA0Si5zxrPhaaY98v5WshmCrerhVi+LmCxvqPiafUslXoYpq0XkuiQ1p4Ax4XQ2BSwdnuYP7p9QlvuG40JHI1lUaenv3o5w3Xvu2jOWU179oQNn5aisNMvLBvDOg==)

</div>

Với slot, `<FancyButton>` chịu trách nhiệm render phần `<button>` bên ngoài (và phần style "fancy"), còn nội dung bên trong do component cha cung cấp.

Một cách khác để hiểu slot là so sánh nó với hàm JavaScript:

```js
// component cha truyền nội dung slot
FancyButton('Click me!')

// FancyButton render nội dung slot trong template của chính nó
function FancyButton(slotContent) {
  return `<button class="fancy-btn">
      ${slotContent}
    </button>`
}
```

Nội dung slot không chỉ giới hạn ở văn bản. Nó có thể là bất kỳ nội dung template hợp lệ nào. Ví dụ, ta có thể truyền nhiều phần tử, hoặc thậm chí cả component khác:

```vue-html
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1UmtOwkAQvspQYtCEgrx81EqCJibeoX+W7bRZaHc3+1AI4QyewH8ewvN4Aa/gbgtNIfFf5+vMfI/ZXbCQcvBmMYiCWFPFpAGNxsp5wlkphTLwQjjdPlljBIdMiRJ6g2EL88O9pnnxjlqU+EpbzS3s0BwPaypH4gqDpSyIQVcBxK3VFQDwXDC6hhJdlZi4zf3fRKwl4aDNtsDHJKCiECqiW8KTYH5c1gEnwnUdJ9rCh/XeM6Z42AgN+sFZAj6+Ux/LOjFaEK2diMz3h0vjNfj/zokuhPFU3lTdfcpShVOZcJ+DZgHs/HxtCrpZlj34eknoOlfC8jSCgnEkKswVSRlyczkZzVLM+9CdjtPJ/RjGswtX3ExvMcuu6mmhUnTruOBYAZKkKeN5BDO5gdG13FRoSVTOeAW2xkLPY3UEdweYWqW9OCkYN6gctq9uXllx2Z09CJ9dJwzBascI7nBYihWDldUGMqEgdTVIq6TQqCEMfUpNSD+fX7/fH+3b7P8AdGP6wA==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNptUltu2zAQvMpGQZEWsOzGiftQ1QBpgQK9g35oaikwkUiCj9aGkTPkBPnLIXKeXCBXyJKKBdoIoA/tYGd3doa74tqY+b+ARVXUjltp/FWj5GC09fCHKb79FbzXCoTVA5zNFxkWaWdT8/V/dHrAvzxrzrC3ZoBG4SYRWhQs9B52EeWapihU3lWwyxfPDgbfNYq+ejEppcLjYHrmkSqAOqMmAOB3L/ktDEhV4+v8gMR/l1M7wxQ4v+3xZ1Nw3Wtb8S1TTXG1H3cCJIO69oxc5mLUcrSrXkxSi1lxZGT0//CS9Wg875lzJELE/nLto4bko69dr31cFc8auw+3JHvSEfQ7nwbsHY9HwakQ4kes14zfdlYH1VbQS4XMlp1lraRMPl6cr1rsZnB6uWwvvi9hufpAxZfLryjEp5GtbYs0TlGICTCsbaXqKliZDZx/NpuEDsx2UiUwo5VxT6Dkv73BPFgXxRktlUdL2Jh6OoW8O3pX0buTsoTgaCNQcDjoGwk3wXkQ2tJLGzSYYI126KAso0uTSc8Pjy9P93k2d6+NyRKa)

</div>

Nhờ slot, `<FancyButton>` trở nên linh hoạt và có thể tái sử dụng hơn. Giờ ta có thể dùng nó ở nhiều nơi với phần nội dung bên trong khác nhau nhưng vẫn giữ cùng một kiểu hiển thị "fancy".

Cơ chế slot của component Vue được lấy cảm hứng từ [phần tử `<slot>` của Web Component gốc](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot), nhưng có thêm một số khả năng mở rộng mà ta sẽ thấy ngay sau đây.

## Phạm Vi Render {#render-scope}

Nội dung slot có thể truy cập scope dữ liệu của component cha, vì nó được định nghĩa ở component cha. Ví dụ:

```vue-html
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```

Ở đây, cả hai biểu thức nội suy <span v-pre>`{{ message }}`</span> đều sẽ render cùng một nội dung.

Nội dung slot **không** có quyền truy cập vào dữ liệu của component con. Biểu thức trong template Vue chỉ có thể truy cập scope mà nó được định nghĩa trong đó, tương tự như lexical scope của JavaScript. Nói cách khác:

> Biểu thức trong template của component cha chỉ truy cập được scope cha; biểu thức trong template của component con chỉ truy cập được scope con.

## Nội Dung Dự Phòng {#fallback-content}

Có những trường hợp rất hữu ích nếu ta chỉ định nội dung dự phòng (mặc định) cho slot, nội dung này chỉ được render khi không có nội dung nào được truyền vào. Ví dụ, với component `<SubmitButton>`:

```vue-html
<button type="submit">
  <slot></slot>
</button>
```

Ta có thể muốn chữ "Submit" được hiển thị bên trong `<button>` nếu component cha không truyền nội dung slot nào. Để biến "Submit" thành nội dung dự phòng, ta chỉ cần đặt nó giữa cặp thẻ `<slot>`:

```vue-html{3}
<button type="submit">
  <slot>
    Submit <!-- nội dung dự phòng -->
  </slot>
</button>
```

Giờ khi ta dùng `<SubmitButton>` trong component cha mà không truyền nội dung cho slot:

```vue-html
<SubmitButton />
```

Nội dung dự phòng "Submit" sẽ được render:

```html
<button type="submit">Submit</button>
```

Nhưng nếu ta truyền nội dung:

```vue-html
<SubmitButton>Save</SubmitButton>
```

Thì nội dung đó sẽ được render thay thế:

```html
<button type="submit">Save</button>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1kMsKwjAQRX9lzMaNbfcSC/oL3WbT1ikU8yKZFEX8d5MGgi2YVeZxZ86dN7taWy8B2ZlxP7rZEnikYFuhZ2WNI+jCoGa6BSKjYXJGwbFufpNJfhSaN1kflTEgVFb2hDEC4IeqguARpl7KoR8fQPgkqKpc3Wxo1lxRWWeW+Y4wBk9x9V9d2/UL8g1XbOJN4WAntodOnrecQ2agl8WLYH7tFyw5olj10iR3EJ+gPCxDFluj0YS6EAqKR8mi9M3Td1ifLxWShcU=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp1UEEOwiAQ/MrKxYu1d4Mm+gWvXChuk0YKpCyNxvh3lxIb28SEA8zuDDPzEucQ9mNCcRAymqELdFKu64MfCK6p6Tu6JCLvoB18D9t9/Qtm4lY5AOXwMVFu2OpkCV4ZNZ51HDqKhwLAQjIjb+X4yHr+mh+EfbCakF8AclNVkCJCq61ttLkD4YOgqsp0YbGesJkVBj92NwSTIrH3v7zTVY8oF8F4SdazD7ET69S5rqXPpnigZ8CjEnHaVyInIp5G63O6XIGiIlZMzrGMd8RVfR0q4lIKKV+L+srW+wNTTZq3)

</div>

## Slot Có Tên {#named-slots}

Đôi khi một component cần nhiều vị trí slot trong cùng một template. Ví dụ, với component `<BaseLayout>` có template như sau:

```vue-html
<div class="container">
  <header>
    <!-- Ta muốn nội dung header ở đây -->
  </header>
  <main>
    <!-- Ta muốn nội dung chính ở đây -->
  </main>
  <footer>
    <!-- Ta muốn nội dung footer ở đây -->
  </footer>
</div>
```

Trong các trường hợp như vậy, phần tử `<slot>` có một thuộc tính đặc biệt tên là `name`, cho phép gán một ID duy nhất cho từng slot để bạn quyết định nội dung sẽ được render ở đâu:

```vue-html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Một slot outlet không có `name` sẽ ngầm được xem là slot `"default"`.

Trong component cha đang dùng `<BaseLayout>`, ta cần một cách để truyền nhiều đoạn nội dung slot khác nhau, mỗi đoạn nhắm vào một slot outlet khác nhau. Đây chính là lúc **slot có tên** phát huy tác dụng.

Để truyền một slot có tên, ta dùng phần tử `<template>` với directive `v-slot`, sau đó truyền tên slot làm đối số của `v-slot`:

```vue-html
<BaseLayout>
  <template v-slot:header>
    <!-- nội dung cho slot header -->
  </template>
</BaseLayout>
```

`v-slot` có cú pháp rút gọn riêng là `#`, nên `<template v-slot:header>` có thể viết ngắn thành `<template #header>`. Hãy hiểu đây là "render đoạn template này vào slot 'header' của component con".

![named slots diagram](./images/named-slots.png)

<!-- https://www.figma.com/file/2BhP8gVZevttBu9oUmUUyz/named-slot -->

Đây là đoạn mã truyền nội dung cho cả ba slot của `<BaseLayout>` bằng cú pháp rút gọn:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

Khi một component chấp nhận cả default slot và named slot, mọi node cấp cao nhất không phải `<template>` sẽ ngầm được xem là nội dung của default slot. Vì vậy, ví dụ trên cũng có thể viết như sau:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <!-- default slot ngầm -->
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

Khi đó, mọi nội dung bên trong `<template>` sẽ được chuyển vào slot tương ứng. HTML cuối cùng được render sẽ là:

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9UsFuwjAM/RWrHLgMOi5o6jIkdtphn9BLSF0aKU2ixEVjiH+fm8JoQdvRfu/5xS8+ZVvvl4cOsyITUQXtCSJS5zel1a13geBdRvyUR9cR1MG1MF/mt1YvnZdW5IOWVVwQtt5IQq4AxI2cau5ccZg1KCsMlz4jzWrzgQGh1fuGYIcgwcs9AmkyKHKGLyPykcfD1Apr2ZmrHUN+s+U5Qe6D9A3ULgA1bCK1BeUsoaWlyPuVb3xbgbSOaQGcxRH8v3XtHI0X8mmfeYToWkxmUhFoW7s/JvblJLERmj1l0+T7T5tqK30AZWSMb2WW3LTFUGZXp/u8o3EEVrbI9AFjLn8mt38fN9GIPrSp/p4/Yoj7OMZ+A/boN9KInPeZZpAOLNLRDAsPZDgN4p0L/NQFOV/Ayn9x6EZXMFNKvQ4E5YwLBczW6/WlU3NIi6i/sYDn5Qu2qX1OF51MsvMPkrIEHg==)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9UkFuwjAQ/MoqHLiUpFxQlaZI9NRDn5CLSTbEkmNb9oKgiL934wRwQK3ky87O7njGPicba9PDHpM8KXzlpKV1qWVnjSP4FB6/xcnsCRpnOpin2R3qh+alBig1HgO9xkbsFcG5RyvDOzRq8vkAQLSury+l5lNkN1EuCDurBCFXAMWdH2pGrn2YtShqdCPOnXa5/kKH0MldS7BFEGDFDoEkKSwybo8rskjjaevo4L7Wrje8x4mdE7aFxjiglkWE1GxQE9tLi8xO+LoGoQ3THLD/qP2/dGMMxYZs8DP34E2HQUxUBFI35o+NfTlJLOomL8n04frXns7W8gCVEt5/lElQkxpdmVyVHvP2yhBo0SHThx5z+TEZvl1uMlP0oU3nH/kRo3iMI9Ybes960UyRsZ9pBuGDeTqpwfBAvn7NrXF81QUZm8PSHjl0JWuYVVX1PhAqo4zLYbZarUak4ZAWXv5gDq/pG3YBHn50EEkuv5irGBk=)

</div>

Một lần nữa, bạn có thể thấy named slot dễ hiểu hơn nếu so sánh với hàm JavaScript:

```js
// truyền nhiều đoạn slot với các tên khác nhau
BaseLayout({
  header: `...`,
  default: `...`,
  footer: `...`
})

// <BaseLayout> render chúng ở các vị trí khác nhau
function BaseLayout(slots) {
  return `<div class="container">
      <header>${slots.header}</header>
      <main>${slots.default}</main>
      <footer>${slots.footer}</footer>
    </div>`
}
```

## Slot Có Điều Kiện {#conditional-slots}

Đôi khi bạn muốn render một thứ gì đó dựa trên việc slot có được truyền nội dung hay không.

Bạn có thể dùng property [$slots](/api/component-instance#slots) kết hợp với [v-if](/guide/essentials/conditional#v-if) để làm điều đó.

Trong ví dụ dưới đây, ta định nghĩa một component Card có ba slot điều kiện: `header`, `footer` và slot `default`. Khi có nội dung cho header / footer / default, ta sẽ bọc nó lại để bổ sung style:

```vue-html
<template>
  <div class="card">
    <div v-if="$slots.header" class="card-header">
      <slot name="header" />
    </div>

    <div v-if="$slots.default" class="card-content">
      <slot />
    </div>

    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>
```

[Thử trên Playground](https://play.vuejs.org/#eNqVVMtu2zAQ/BWCLZBLIjVoTq4aoA1yaA9t0eaoCy2tJcYUSZCUKyPwv2dJioplOw4C+EDuzM4+ONYT/aZ1tumBLmhhK8O1IxZcr29LyTutjCN3zNRkZVRHLrLcXzz9opRFHvnIxIuDTgvmAG+EFJ4WTnhOCPnQAqvBjHFE2uvbh5Zbgj/XAolwkWN4TM33VI/UalixXvjyo5yeqVVKOpCuyP0ob6utlHL7vUE3U4twkWP4hJq/jiPP4vSSOouNrHiTPVolcclPnl3SSnWaCzC/teNK2pIuSEA8xoRQ/3+GmDM9XKZ41UK1PhF/tIOPlfSPAQtmAyWdMMdMAy7C9/9+wYDnCexU3QtknwH/glWi9z1G2vde1tj2Hi90+yNYhcvmwd4PuHabhvKNeuYu8EuK1rk7M/pLu5+zm5BXyh1uMdnOu3S+95pvSCWYtV9xQcgqaXogj2yu+AqBj1YoZ7NosJLOEq5S9OXtPZtI1gFSppx8engUHs+vVhq9eVhq9ORRrXdpRyseSqfo6SmmnONK6XTw9yis24q448wXSG+0VAb3sSDXeiBoDV6TpWDV+ktENatrdMGCfAoBfL1JYNzzpINJjVFoJ9yKUKho19ul6OFQ6UYPx1rjIpPYeXIc/vXCgjetawzbni0dPnhhJ3T3DMVSruI=)

## Tên Slot Động {#dynamic-slot-names}

[Đối số directive động](/guide/essentials/template-syntax#dynamic-arguments) cũng hoạt động với `v-slot`, cho phép định nghĩa tên slot động:

```vue-html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- cú pháp rút gọn -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

Lưu ý rằng biểu thức này vẫn phải tuân theo [các ràng buộc cú pháp](/guide/essentials/template-syntax#dynamic-argument-syntax-constraints) của đối số directive động.

## Scoped Slot {#scoped-slots}

Như đã bàn trong [Phạm vi render](#render-scope), nội dung slot không thể truy cập state của component con.

Tuy nhiên, có những trường hợp sẽ rất hữu ích nếu nội dung slot có thể dùng cả dữ liệu từ scope cha lẫn scope con. Để làm được điều đó, ta cần một cách để component con truyền dữ liệu vào slot khi render.

Và thực tế ta hoàn toàn làm được điều này: ta có thể truyền thuộc tính cho slot outlet giống như truyền props cho component:

```vue-html
<!-- template của <MyComponent> -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```

Việc nhận slot props hơi khác nhau giữa trường hợp chỉ có một default slot và trường hợp có named slot. Ta sẽ bắt đầu với cách nhận props cho một default slot duy nhất, bằng cách dùng `v-slot` trực tiếp trên thẻ component con:

```vue-html
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```

![scoped slots diagram](./images/scoped-slots.svg)

<!-- https://www.figma.com/file/QRneoj8eIdL1kw3WQaaEyc/scoped-slot -->

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNp9kMEKgzAMhl8l9OJlU3aVOhg7C3uAXsRlTtC2tFE2pO++dA5xMnZqk+b/8/2dxMnadBxQ5EL62rWWwCMN9qh021vjCMrn2fBNoya4OdNDkmarXhQnSstsVrOOC8LedhVhrEiuHca97wwVSsTj4oz1SvAUgKJpgqWZEj4IQoCvZm0Gtgghzss1BDvIbFkqdmID+CNdbbQnaBwitbop0fuqQSgguWPXmX+JePe1HT/QMtJBHnE51MZOCcjfzPx04JxsydPzp2Szxxo7vABY1I/p)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqFkNFqxCAQRX9l8CUttAl9DbZQ+rzQD/AlJLNpwKjoJGwJ/nvHpAnusrAg6FzHO567iE/nynlCUQsZWj84+lBmGJ31BKffL8sng4bg7O0IRVllWnpWKAOgDF7WBx2em0kTLElt975QbwLkhkmIyvCS1TGXC8LR6YYwVSTzH8yvQVt6VyJt3966oAR38XhaFjjEkvBCECNcia2d2CLyOACZQ7CDrI6h4kXcAF7lcg+za6h5et4JPdLkzV4B9B6RBtOfMISmxxqKH9TarrGtATxMgf/bDfM/qExEUCdEDuLGXAmoV06+euNs2JK7tyCrzSNHjX9aurQf)

</div>

Slot props do component con truyền vào slot sẽ khả dụng dưới dạng giá trị của directive `v-slot` tương ứng, và có thể được truy cập bởi các biểu thức bên trong nội dung slot.

Bạn có thể xem scoped slot như một hàm được truyền vào component con. Component con sau đó sẽ gọi hàm đó và truyền props làm đối số:

```js
MyComponent({
  // truyền default slot nhưng dưới dạng một hàm
  default: (slotProps) => {
    return `${slotProps.text} ${slotProps.count}`
  }
})

function MyComponent(slots) {
  const greetingMessage = 'hello'
  return `<div>${
    // gọi hàm slot với props!
    slots.default({ text: greetingMessage, count: 1 })
  }</div>`
}
```

Thực ra, điều này rất gần với cách scoped slot được biên dịch, và cũng gần với cách bạn sẽ dùng scoped slot trong [render function](/guide/extras/render-function) viết tay.

Hãy chú ý rằng `v-slot="slotProps"` khớp với chữ ký hàm của slot. Cũng giống như đối số của hàm, ta có thể dùng destructuring trong `v-slot`:

```vue-html
<MyComponent v-slot="{ text, count }">
  {{ text }} {{ count }}
</MyComponent>
```

### Named Scoped Slot {#named-scoped-slots}

Named scoped slot hoạt động tương tự, tức là slot props sẽ có trong giá trị của directive `v-slot`: `v-slot:name="slotProps"`. Khi dùng cú pháp rút gọn, nó trông như sau:

```vue-html
<MyComponent>
  <template #header="headerProps">
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>
```

Truyền props vào named slot:

```vue-html
<slot name="header" message="hello"></slot>
```

Lưu ý rằng `name` của slot sẽ không nằm trong props vì nó là từ khóa dành riêng, nên `headerProps` ở ví dụ trên sẽ là `{ message: 'hello' }`.

Nếu bạn trộn named slot với default scoped slot, bạn cần dùng thẻ `<template>` tường minh cho default slot. Nếu cố đặt directive `v-slot` trực tiếp trên component, trình biên dịch sẽ báo lỗi. Mục đích là để tránh mọi mơ hồ về phạm vi của props thuộc default slot. Ví dụ:

```vue-html
<!-- template của <MyComponent> -->
<div>
  <slot :message="hello"></slot>
  <slot name="footer" />
</div>
```

```vue-html
<!-- Template này sẽ không biên dịch được -->
<MyComponent v-slot="{ message }">
  <p>{{ message }}</p>
  <template #footer>
    <!-- message thuộc về default slot và không khả dụng ở đây -->
    <p>{{ message }}</p>
  </template>
</MyComponent>
```

Dùng thẻ `<template>` tường minh cho default slot sẽ giúp làm rõ rằng prop `message` không khả dụng bên trong slot khác:

```vue-html
<MyComponent>
  <!-- Dùng default slot tường minh -->
  <template #default="{ message }">
    <p>{{ message }}</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</MyComponent>
```

### Ví Dụ Fancy List {#fancy-list-example}

Bạn có thể đang tự hỏi đâu là trường hợp sử dụng tốt cho scoped slot. Đây là một ví dụ: hãy tưởng tượng một component `<FancyList>` render ra một danh sách item. Nó có thể bao bọc logic tải dữ liệu từ xa, dùng dữ liệu để hiển thị danh sách, thậm chí cả các tính năng nâng cao như phân trang hoặc infinite scrolling. Tuy nhiên, ta vẫn muốn nó linh hoạt về cách hiển thị từng item và giao việc định kiểu cho component cha đang dùng nó. Khi đó, cách dùng mong muốn có thể sẽ như sau:

```vue-html
<FancyList :api-url="url" :per-page="10">
  <template #item="{ body, username, likes }">
    <div class="item">
      <p>{{ body }}</p>
      <p>by {{ username }} | {{ likes }} likes</p>
    </div>
  </template>
</FancyList>
```

Bên trong `<FancyList>`, ta có thể render cùng một `<slot>` nhiều lần với dữ liệu item khác nhau (hãy chú ý rằng ta dùng `v-bind` để truyền cả object làm slot props):

```vue-html
<ul>
  <li v-for="item in items">
    <slot name="item" v-bind="item"></slot>
  </li>
</ul>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqFU2Fv0zAQ/StHJtROapNuZTBCNwnQQKBpTGxCQss+uMml8+bYlu2UlZL/zjlp0lQa40sU3/nd3Xv3vA7eax0uSwziYGZTw7UDi67Up4nkhVbGwScm09U5tw5yowoYhFEX8cBBImdRgyQMHRwWWjCHdAKYbdFM83FpxEkS0DcJINZoxpotkCIHkySo7xOixcMep19KrmGustUISotGsgJHIPgDWqg6DKEyvoRUMGsJ4HG9HGX16bqpAlU1izy5baqDFegYweYroMttMwLAHx/Y9Kyan36RWUTN2+mjXfpbrei8k6SjdSuBYFOlMaNI6AeAtcflSrqx5b8xhkl4jMU7H0yVUCaGvVeH8+PjKYWqWnpf5DQYBTtb+fc612Awh2qzzGaBiUyVpBVpo7SFE8gw5xIv/Wl4M9gsbjCCQbuywe3+FuXl9iiqO7xpElEEhUofKFQo2mTGiFiOLr3jcpFImuiaF6hKNxzuw8lpw7kuEy6ZKJGK3TR6NluLYXBVqwRXQjkLn0ueIc3TLonyZ0sm4acqKVovKIbDCVQjGsb1qvyg2telU4Yzz6eHv6ARBWdwjVqUNCbbFjqgQn6aW1J8RKfJhDg+5/lStG4QHJZjnpO5XjT0BMqFu+uZ81yxjEQJw7A1kOA76FyZjaWBy0akvu8tCQKeQ+d7wsy5zLpz1FlzU3kW1QP+x40ApWgWAySEJTv6/NitNMkllcTakwCaZZ5ADEf6cROas/RhYVQps5igEpkZLwzRROmG04OjDBcj7+Js+vYQDo9e0uH1qzeY5/s1vtaaqG969+vTTrsmBTMLLv12nuy7l+d5W673SBzxkzlfhPdWSXokdZMkSFWhuUDzTTtOnk6CuG2fBEwI9etrHXOmRLJUE0/vMH14In5vH30sCS4Nkr+WmARdztHQ6Jr02dUFPtJ/lyxUVgq6/UzyO1olSj9jc+0DcaWxe/fqab/UT51Uu7Znjw6lbUn5QWtR6vtJQM//4zPUt+NOw+lGzCqo/gLm1QS8)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNVNtq20AQ/ZWpQnECujhO0qaqY+hD25fQl4RCifKwllbKktXushcT1/W/d1bSSnYJNCCEZmbPmcuZ1S76olS6cTTKo6UpNVN2VQjWKqktfCOi3N4yY6HWsoVZmo0eD5kVAqAQ9KU7XNGaOG5h572lRAZBhTV574CJzJv7QuCzzMaMaFjaKk4sRQtgOeUmiiVO85siwncRQa6oThRpKHrO50XUnUdEwMMJw08M7mAtq20MzlAtSEtj4OyZGkweMIiq2AZKToxBgMcdxDCqVrueBfb7ZaaOQiOspZYgbL0FPBySIQD+eMeQc99/HJIsM0weqs+O258mjfZREE1jt5yCKaWiFXpSX0A/5loKmxj2m+YwT69p+7kXg0udw8nlYn19fYGufvSeZBXF0ZGmR2vwmrJKS4WiPswGWWYxzIIgs8fYH6mIJadnQXdNrdMiWAB+yJ7gsXdgLfjqcK10wtJqgmYZ+spnpGgl6up5oaa2fGKi6U8Yau9ZS6Wzpwi7WU1p7BMzaZcLbuBh0q2XM4fZXTc+uOPSGvjuWEWxlaAexr9uiIBf0qG3Uy6HxXwo9B+mn47CvbNSM+LHccDxAyvmjMA9Vdxh1WQiO0eywBVGEaN3Pj972wVxPKwOZ7BJWI2b+K5rOOVUNPbpYJNvJalwZmmahm3j7AhdSz3sPzDRS3R4SQwOCXxP4yVBzJqJarSzcY8H5mXWFfif1QVwPGjGcQWTLp7YrcLxCfyDdAuMW0cq30AOV+plcK1J+dxoXJkqR6igRCeNxjbxp3N6cX5V0Sb2K19dfFrA4uo9Gh8uP9K6Puvw3eyx9SH3IT/qPCZpiW6Y8Gq9mvekrutAN96o/V99ALPj)

</div>

### Renderless Component {#renderless-components}

Trường hợp của `<FancyList>` bao bọc cả logic tái sử dụng được (lấy dữ liệu, phân trang, v.v.) lẫn phần hiển thị, đồng thời giao một phần hiển thị cho component tiêu thụ thông qua scoped slot.

Nếu đẩy ý tưởng này đi xa hơn nữa, ta có thể tạo ra những component chỉ bao bọc logic và hoàn toàn không tự render gì cả, phần hiển thị sẽ được giao trọn vẹn cho component tiêu thụ thông qua scoped slot. Ta gọi kiểu component này là **Renderless Component**.

Một ví dụ về renderless component có thể là component bao bọc logic theo dõi vị trí chuột hiện tại:

```vue-html
<MouseTracker v-slot="{ x, y }">
  Mouse is at: {{ x }}, {{ y }}
</MouseTracker>
```

<div class="composition-api">

[Thử trên Playground](https://play.vuejs.org/#eNqNUcFqhDAQ/ZUhF12w2rO4Cz301t5aaCEX0dki1SQko6uI/96J7i4qLPQQmHmZ9+Y9ZhQvxsRdiyIVmStsZQgcUmtOUlWN0ZbgXbcOP2xe/KKFs9UNBHGyBj09kCpLFj4zuSFsTJ0T+o6yjUb35GpNRylG6CMYYJKCpwAkzWNQOcgphZG/YZoiX/DQNAttFjMrS+6LRCT2rh6HGsHiOQKtmKIIS19+qmZpYLrmXIKxM1Vo5Yj9HD0vfD7ckGGF3LDWlOyHP/idYPQCfdzldTtjscl/8MuDww78lsqHVHdTYXjwCpdKlfoS52X52qGit8oRKrRhwHYdNrrDILouPbCNVZCtgJ1n/6Xx8JYAmT8epD3fr5cC0oGLQYpkd4zpD27R0vA=)

</div>
<div class="options-api">

[Thử trên Playground](https://play.vuejs.org/#eNqVUU1rwzAM/SvCl7SQJTuHdLDDbttthw18MbW6hjW2seU0oeS/T0lounQfUDBGepaenvxO4tG5rIkoClGGra8cPUhT1c56ghcbA756tf1EDztva0iy/Ds4NCbSAEiD7diicafigeA0oFvLPAYNhWICYEE5IL00fMp8Hs0JYe0OinDIqFyIaO7CwdJGihO0KXTcLriK59NYBlUARTyMn6Hv0yHgIp7ARAvl3FXm8yCRiuu1Fv/x23JakVqtz3t5pOjNOQNoC7hPz0nHyRSzEr7Ghxppb/XlZ6JjRlzhTAlA+ypkLWwAM6c+8G2BdzP+/pPbRkOoL/KOldH2mCmtnxr247kKhAb9KuHKgLVtMEkn2knG+sIVzV9sfmy8hfB/swHKwV0oWja4lQKKjoNOivzKrf4L/JPqaQ==)

</div>

Dù là một pattern thú vị, phần lớn những gì có thể đạt được bằng Renderless Component cũng có thể đạt được theo cách hiệu quả hơn bằng Composition API mà không phải chịu thêm overhead của việc lồng nhiều component. Về sau, ta sẽ thấy cách triển khai cùng chức năng theo dõi chuột này dưới dạng một [Composable](/guide/reusability/composables).

Tuy vậy, scoped slot vẫn rất hữu ích trong những trường hợp ta cần vừa bao bọc logic **vừa** kết hợp phần hiển thị, như ví dụ `<FancyList>` ở trên.
