# Options: State {#options-state}

## data {#data}

Hàm trả về state phản ứng ban đầu cho instance component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    data?(
      this: ComponentPublicInstance,
      vm: ComponentPublicInstance
    ): object
  }
  ```

- **Chi tiết**

  Hàm dự kiến trả về một plain JavaScript object, sẽ được Vue làm cho có tính phản ứng. Sau khi instance được tạo, data object phản ứng có thể được truy cập là `this.$data`. Instance component cũng ủy quyền tất cả thuộc tính tìm thấy trên data object, vì vậy `this.a` sẽ tương đương với `this.$data.a`.

  Tất cả thuộc tính data cấp cao nhất phải được đưa vào data object được trả về. Thêm thuộc tính mới vào `this.$data` là có thể, nhưng **không** khuyến nghị. Nếu giá trị mong muốn của một thuộc tính chưa có sẵn thì nên đưa vào một giá trị rỗng như `undefined` hoặc `null` làm placeholder để đảm bảo Vue biết thuộc tính đó tồn tại.

  Các thuộc tính bắt đầu bằng `_` hoặc `$` sẽ **không** được ủy quyền trên instance component vì chúng có thể xung đột với các thuộc tính nội bộ và phương thức API của Vue. Bạn sẽ phải truy cập chúng là `this.$data._property`.

  **Không** khuyến nghị trả về các object có hành vi có trạng thái riêng như các object browser API và thuộc tính prototype. Object được trả về lý tưởng nhất là một plain object chỉ biểu diễn state của component.

- **Ví dụ**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    created() {
      console.log(this.a) // 1
      console.log(this.$data) // { a: 1 }
    }
  }
  ```

  Lưu ý nếu bạn dùng arrow function với thuộc tính `data`, `this` sẽ không phải instance của component, nhưng bạn vẫn có thể truy cập instance là đối số đầu tiên của hàm:

  ```js
  data: (vm) => ({ a: vm.myProp })
  ```

- **Xem thêm** [Tính Phản ứng Chuyên sâu](/guide/extras/reactivity-in-depth)

## props {#props}

Khai báo các prop của một component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    props?: ArrayPropsOptions | ObjectPropsOptions
  }

  type ArrayPropsOptions = string[]

  type ObjectPropsOptions = { [key: string]: Prop }

  type Prop<T = any> = PropOptions<T> | PropType<T> | null

  interface PropOptions<T> {
    type?: PropType<T>
    required?: boolean
    default?: T | ((rawProps: object) => T)
    validator?: (value: unknown, rawProps: object) => boolean
  }

  type PropType<T> = { new (): T } | { new (): T }[]
  ```

  > Các kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  Trong Vue, tất cả prop của component cần được khai báo rõ ràng. Prop component có thể được khai báo ở hai dạng:

  - Dạng đơn giản dùng mảng chuỗi
  - Dạng đầy đủ dùng object trong đó mỗi key thuộc tính là tên của prop, và giá trị là kiểu của prop (hàm constructor) hoặc các tùy chọn nâng cao.

  Với cú pháp dựa trên object, mỗi prop có thể định nghĩa thêm các tùy chọn sau:

  - **`type`**: Có thể là một trong các constructor native sau: `String`, `Number`, `Boolean`, `Array`, `Object`, `Date`, `Function`, `Symbol`, bất kỳ hàm constructor tùy chỉnh nào hoặc mảng của những cái đó. Trong chế độ development, Vue sẽ kiểm tra xem giá trị của prop có khớp với kiểu được khai báo không, và sẽ ném cảnh báo nếu không. Xem [Xác thực Prop](/guide/components/props#prop-validation) để biết thêm chi tiết.

    Cũng lưu ý rằng prop có kiểu `Boolean` ảnh hưởng đến hành vi ép kiểu giá trị của nó trong cả development và production. Xem [Boolean Casting](/guide/components/props#boolean-casting) để biết thêm chi tiết.

  - **`default`**: Chỉ định giá trị mặc định cho prop khi nó không được truyền bởi cha hoặc có giá trị `undefined`. Mặc định object hoặc array phải được trả về bằng hàm factory. Hàm factory cũng nhận object props thô là đối số.

  - **`required`**: Định nghĩa liệu prop có bắt buộc không. Trong môi trường không phải production, cảnh báo console sẽ được ném nếu giá trị này là truthy và prop không được truyền.

  - **`validator`**: Hàm validator tùy chỉnh nhận giá trị prop và props object làm đối số. Trong chế độ development, cảnh báo console sẽ được ném nếu hàm này trả về giá trị falsy (tức là xác thực thất bại).

- **Ví dụ**

  Khai báo đơn giản:

  ```js
  export default {
    props: ['size', 'myMessage']
  }
  ```

  Khai báo object với xác thực:

  ```js
  export default {
    props: {
      // kiểm tra kiểu
      height: Number,
      // kiểm tra kiểu cộng các xác thực khác
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: (value) => {
          return value >= 0
        }
      }
    }
  }
  ```

- **Xem thêm**
  - [Hướng dẫn - Props](/guide/components/props)
  - [Hướng dẫn - Khai báo Kiểu cho Props của Component](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

## computed {#computed}

Khai báo các thuộc tính computed để expose trên instance component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    computed?: {
      [key: string]: ComputedGetter<any> | WritableComputedOptions<any>
    }
  }

  type ComputedGetter<T> = (
    this: ComponentPublicInstance,
    vm: ComponentPublicInstance
  ) => T

  type ComputedSetter<T> = (
    this: ComponentPublicInstance,
    value: T
  ) => void

  type WritableComputedOptions<T> = {
    get: ComputedGetter<T>
    set: ComputedSetter<T>
  }
  ```

- **Chi tiết**

  Tùy chọn nhận một object trong đó key là tên của thuộc tính computed, và giá trị là computed getter, hoặc object có phương thức `get` và `set` (cho thuộc tính computed có thể ghi).

  Tất cả getter và setter đều có context `this` được tự động ràng buộc với instance component.

  Lưu ý nếu bạn dùng arrow function với thuộc tính computed, `this` sẽ không trỏ đến instance của component, nhưng bạn vẫn có thể truy cập instance là đối số đầu tiên của hàm:

  ```js
  export default {
    computed: {
      aDouble: (vm) => vm.a * 2
    }
  }
  ```

- **Ví dụ**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    computed: {
      // readonly
      aDouble() {
        return this.a * 2
      },
      // có thể ghi
      aPlus: {
        get() {
          return this.a + 1
        },
        set(v) {
          this.a = v - 1
        }
      }
    },
    created() {
      console.log(this.aDouble) // => 2
      console.log(this.aPlus) // => 2

      this.aPlus = 3
      console.log(this.a) // => 2
      console.log(this.aDouble) // => 4
    }
  }
  ```

- **Xem thêm**
  - [Hướng dẫn - Thuộc tính Computed](/guide/essentials/computed)
  - [Hướng dẫn - Khai báo Kiểu cho Thuộc tính Computed](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

## methods {#methods}

Khai báo các phương thức để trộn vào instance component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    methods?: {
      [key: string]: (this: ComponentPublicInstance, ...args: any[]) => any
    }
  }
  ```

- **Chi tiết**

  Các phương thức được khai báo có thể được truy cập trực tiếp trên instance component, hoặc dùng trong biểu thức template. Tất cả phương thức đều có context `this` được tự động ràng buộc với instance component, ngay cả khi được truyền đi.

  Tránh dùng arrow function khi khai báo method, vì chúng sẽ không có quyền truy cập vào instance component qua `this`.

- **Ví dụ**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    methods: {
      plus() {
        this.a++
      }
    },
    created() {
      this.plus()
      console.log(this.a) // => 2
    }
  }
  ```

- **Xem thêm** [Xử lý Sự kiện](/guide/essentials/event-handling)

## watch {#watch}

Khai báo các watch callback được gọi khi data thay đổi.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    watch?: {
      [key: string]: WatchOptionItem | WatchOptionItem[]
    }
  }

  type WatchOptionItem = string | WatchCallback | ObjectWatchOptionItem

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type ObjectWatchOptionItem = {
    handler: WatchCallback | string
    immediate?: boolean // mặc định: false
    deep?: boolean // mặc định: false
    flush?: 'pre' | 'post' | 'sync' // mặc định: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }
  ```

  > Các kiểu được đơn giản hóa để dễ đọc.

- **Chi tiết**

  Tùy chọn `watch` nhận một object trong đó key là các thuộc tính instance component phản ứng cần watch (ví dụ: thuộc tính được khai báo qua `data` hoặc `computed`) - và giá trị là callback tương ứng. Callback nhận giá trị mới và giá trị cũ của nguồn được watch.

  Ngoài thuộc tính cấp cao nhất, key cũng có thể là đường dẫn phân cách bằng dấu chấm đơn giản, ví dụ `a.b.c`. Lưu ý cách dùng này **không** hỗ trợ biểu thức phức tạp - chỉ các đường dẫn phân cách bằng dấu chấm được hỗ trợ. Nếu bạn cần watch các nguồn dữ liệu phức tạp, hãy dùng API [`$watch()`](/api/component-instance#watch) bắt buộc thay thế.

  Giá trị cũng có thể là chuỗi tên phương thức (được khai báo qua `methods`), hoặc object chứa các tùy chọn bổ sung. Khi dùng cú pháp object, callback nên được khai báo dưới trường `handler`. Các tùy chọn bổ sung bao gồm:

  - **`immediate`**: kích hoạt callback ngay lập tức khi tạo watcher. Giá trị cũ sẽ là `undefined` ở lần gọi đầu tiên.
  - **`deep`**: buộc duyệt sâu nguồn nếu nó là object hoặc array, để callback được kích hoạt khi có thay đổi sâu. Xem [Deep Watcher](/guide/essentials/watchers#deep-watchers).
  - **`flush`**: điều chỉnh thời điểm flush của callback. Xem [Thời điểm Flush Callback](/guide/essentials/watchers#callback-flush-timing) và [`watchEffect()`](/api/reactivity-core#watcheffect).
  - **`onTrack / onTrigger`**: debug các dependency của watcher. Xem [Debug Watcher](/guide/extras/reactivity-in-depth#watcher-debugging).

  Tránh dùng arrow function khi khai báo watch callback vì chúng sẽ không có quyền truy cập vào instance component qua `this`.

- **Ví dụ**

  ```js
  export default {
    data() {
      return {
        a: 1,
        b: 2,
        c: {
          d: 4
        },
        e: 5,
        f: 6
      }
    },
    watch: {
      // watch thuộc tính cấp cao nhất
      a(val, oldVal) {
        console.log(`new: ${val}, old: ${oldVal}`)
      },
      // tên phương thức chuỗi
      b: 'someMethod',
      // callback sẽ được gọi bất cứ khi nào bất kỳ thuộc tính object được watch thay đổi bất kể độ sâu lồng nhau của chúng
      c: {
        handler(val, oldVal) {
          console.log('c changed')
        },
        deep: true
      },
      // watch một thuộc tính lồng nhau đơn lẻ:
      'c.d': function (val, oldVal) {
        // làm gì đó
      },
      // callback sẽ được gọi ngay sau khi bắt đầu quan sát
      e: {
        handler(val, oldVal) {
          console.log('e changed')
        },
        immediate: true
      },
      // bạn có thể truyền mảng callback, chúng sẽ được gọi lần lượt
      f: [
        'handle1',
        function handle2(val, oldVal) {
          console.log('handle2 triggered')
        },
        {
          handler: function handle3(val, oldVal) {
            console.log('handle3 triggered')
          }
          /* ... */
        }
      ]
    },
    methods: {
      someMethod() {
        console.log('b changed')
      },
      handle1() {
        console.log('handle 1 triggered')
      }
    },
    created() {
      this.a = 3 // => new: 3, old: 1
    }
  }
  ```

- **Xem thêm** [Watcher](/guide/essentials/watchers)

## emits {#emits}

Khai báo các custom event được emit bởi component.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    emits?: ArrayEmitsOptions | ObjectEmitsOptions
  }

  type ArrayEmitsOptions = string[]

  type ObjectEmitsOptions = { [key: string]: EmitValidator | null }

  type EmitValidator = (...args: unknown[]) => boolean
  ```

- **Chi tiết**

  Sự kiện được emit có thể được khai báo ở hai dạng:

  - Dạng đơn giản dùng mảng chuỗi
  - Dạng đầy đủ dùng object trong đó mỗi key thuộc tính là tên của sự kiện, và giá trị là `null` hoặc hàm validator.

  Hàm validator sẽ nhận các đối số bổ sung được truyền vào lời gọi `$emit` của component. Ví dụ, nếu `this.$emit('foo', 1)` được gọi, validator tương ứng cho `foo` sẽ nhận đối số `1`. Hàm validator nên trả về boolean để chỉ ra liệu các đối số sự kiện có hợp lệ không.

  Lưu ý tùy chọn `emits` ảnh hưởng đến việc event listener nào được coi là component event listener, thay vì native DOM event listener. Các listener cho sự kiện được khai báo sẽ bị xóa khỏi object `$attrs` của component, vì vậy chúng sẽ không được truyền qua element gốc của component. Xem [Thuộc tính Kế thừa](/guide/components/attrs) để biết thêm chi tiết.

- **Ví dụ**

  Cú pháp mảng:

  ```js
  export default {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  }
  ```

  Cú pháp object:

  ```js
  export default {
    emits: {
      // không có xác thực
      click: null,

      // có xác thực
      submit: (payload) => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Payload sự kiện submit không hợp lệ!`)
          return false
        }
      }
    }
  }
  ```

- **Xem thêm**
  - [Hướng dẫn - Thuộc tính Kế thừa](/guide/components/attrs)
  - [Hướng dẫn - Khai báo Kiểu cho Emits của Component](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

## expose {#expose}

Khai báo các thuộc tính công khai được expose khi instance component được cha truy cập qua template ref.

- **Kiểu**

  ```ts
  interface ComponentOptions {
    expose?: string[]
  }
  ```

- **Chi tiết**

  Mặc định, instance component expose tất cả thuộc tính instance cho cha khi được truy cập qua `$parent`, `$root`, hoặc template ref. Điều này có thể không mong muốn, vì component hầu như có state nội bộ hoặc method nên được giữ private để tránh coupling chặt chẽ.

  Tùy chọn `expose` nhận danh sách chuỗi tên thuộc tính. Khi `expose` được dùng, chỉ các thuộc tính được liệt kê rõ ràng mới được expose trên instance công khai của component.

  `expose` chỉ ảnh hưởng đến các thuộc tính do người dùng định nghĩa - nó không lọc ra các thuộc tính instance component dựng sẵn.

- **Ví dụ**

  ```js
  export default {
    // chỉ `publicMethod` sẽ có sẵn trên instance công khai
    expose: ['publicMethod'],
    methods: {
      publicMethod() {
        // ...
      },
      privateMethod() {
        // ...
      }
    }
  }
  ```
