<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>
<style>
.lambdatest {
  background-color: var(--vt-c-bg-soft);
  border-radius: 8px;
  padding: 12px 16px 12px 12px;
  font-size: 13px;
  a {
    display: flex;
    color: var(--vt-c-text-2);
  }
  img {
    background-color: #fff;
    padding: 12px 16px;
    border-radius: 6px;
    margin-right: 24px;
  }
  .testing-partner {
    color: var(--vt-c-text-1);
    font-size: 15px;
    font-weight: 600;
  }
}
</style>

# Kiểm Thử {#testing}

## Vì Sao Nên Kiểm Thử? {#why-test}

Kiểm thử tự động giúp bạn và cả nhóm xây dựng ứng dụng Vue phức tạp nhanh hơn và tự tin hơn bằng cách ngăn hồi quy, đồng thời khuyến khích bạn tách ứng dụng thành những hàm, module, class và component có thể kiểm thử được. Ứng dụng Vue cũng như mọi ứng dụng khác — có thể hỏng theo nhiều cách, và điều quan trọng là bạn phải phát hiện cũng như sửa được những vấn đề đó trước khi phát hành.

Trong hướng dẫn này, chúng ta sẽ đi qua các thuật ngữ cơ bản và đưa ra khuyến nghị về công cụ nên chọn cho ứng dụng Vue 3.

Có một mục dành riêng về composable trong Vue. Xem [Kiểm Thử Composable](#testing-composables) bên dưới để biết thêm chi tiết.

## Khi Nào Nên Kiểm Thử {#when-to-test}

Hãy bắt đầu kiểm thử càng sớm càng tốt. Chúng tôi khuyến nghị bạn viết test ngay khi có thể. Càng để muộn, ứng dụng sẽ càng có nhiều dependency và việc bắt đầu sẽ càng khó hơn.

## Các Loại Kiểm Thử {#testing-types}

Khi thiết kế chiến lược kiểm thử cho ứng dụng Vue, bạn nên tận dụng các loại kiểm thử sau:

- **Đơn vị**: Kiểm tra xem đầu vào của một hàm, class hoặc composable cụ thể có tạo ra đầu ra hoặc side effect như mong đợi hay không.
- **Component**: Kiểm tra xem component có mount được, render được, có thể tương tác được và hoạt động đúng như mong đợi hay không. Những bài kiểm thử này import nhiều mã hơn unit test, phức tạp hơn và cần nhiều thời gian chạy hơn.
- **End-to-end**: Kiểm tra các tính năng trải dài qua nhiều trang và thực hiện request mạng thật với ứng dụng Vue đã được build cho production. Những bài kiểm thử này thường cần dựng cơ sở dữ liệu hoặc backend khác.

Mỗi loại kiểm thử đều có vai trò trong chiến lược kiểm thử của ứng dụng, và mỗi loại sẽ bảo vệ bạn trước những kiểu sự cố khác nhau.

## Tổng Quan {#overview}

Chúng ta sẽ lần lượt nói ngắn gọn từng loại là gì, có thể triển khai ra sao cho ứng dụng Vue, và đưa ra một số khuyến nghị chung.

## Kiểm Thử Đơn Vị {#unit-testing}

Unit test được viết để xác minh rằng những đơn vị mã nhỏ, tách biệt đang hoạt động đúng như mong đợi. Một unit test thường bao phủ một hàm, class, composable hoặc module duy nhất. Unit test tập trung vào tính đúng đắn về mặt logic và chỉ quan tâm đến một phần nhỏ trong toàn bộ chức năng của ứng dụng. Chúng có thể mock phần lớn môi trường của ứng dụng (ví dụ: state ban đầu, class phức tạp, module bên thứ ba và request mạng).

Nói chung, unit test sẽ bắt được các vấn đề trong business logic và tính đúng đắn về mặt logic của một hàm.

Lấy ví dụ hàm `increment` sau:

```js [helpers.js]
export function increment(current, max = 10) {
  if (current < max) {
    return current + 1
  }
  return current
}
```

Hàm này rất khép kín, nên ta có thể dễ dàng gọi nó và kiểm tra giá trị trả về — đây là ứng viên lý tưởng để viết unit test.

Nếu bất kỳ assertion nào dưới đây thất bại, ta sẽ biết rõ vấn đề nằm ngay trong hàm `increment`.

```js{3-15} [helpers.spec.js]
import { increment } from './helpers'

describe('increment', () => {
  test('increments the current number by 1', () => {
    expect(increment(0, 10)).toBe(1)
  })

  test('does not increment the current number over the max', () => {
    expect(increment(10, 10)).toBe(10)
  })

  test('has a default max of 10', () => {
    expect(increment(10)).toBe(10)
  })
})
```

Như đã đề cập trước đó, unit test thường được áp dụng cho business logic, component, class, module hoặc hàm có tính tự chứa, không liên quan đến render UI, request mạng hoặc các mối quan tâm về môi trường khác.

Thông thường đây là các module JavaScript / TypeScript thuần, không gắn trực tiếp với Vue. Nhìn chung, viết unit test cho business logic trong ứng dụng Vue không khác biệt đáng kể so với ứng dụng dùng framework khác.

Có hai trường hợp mà bạn **nên** unit test những tính năng đặc thù của Vue:

1. Composable
2. Component

### Composable {#composables}

Một nhóm hàm đặc thù của ứng dụng Vue là [Composable](/guide/reusability/composables), và chúng có thể cần cách xử lý riêng khi kiểm thử.
Xem [Kiểm Thử Composable](#testing-composables) bên dưới để biết thêm chi tiết.

### Unit Test Cho Component {#unit-testing-components}

Component có thể được kiểm thử theo hai cách:

1. Hộp trắng: Kiểm thử đơn vị

   Những bài kiểm thử kiểu "hộp trắng" biết rõ chi tiết triển khai và dependency của component. Chúng tập trung vào việc **cô lập** component đang được kiểm thử. Những bài kiểm thử này thường sẽ mock một phần hoặc toàn bộ component con, đồng thời thiết lập state và dependency của plugin (ví dụ: Pinia).

2. Hộp đen: Kiểm thử component

   Những bài kiểm thử kiểu "hộp đen" không biết về chi tiết triển khai của component. Chúng mock ở mức tối thiểu để kiểm tra khả năng tích hợp giữa component và toàn bộ hệ thống. Chúng thường render toàn bộ component con và được xem gần với "integration test" hơn. Xem [khuyến nghị về Kiểm Thử Component](#component-testing) bên dưới.

### Khuyến Nghị {#recommendation}

- [Vitest](https://vitest.dev/)

  Vì thiết lập chính thức do `create-vue` tạo ra dựa trên [Vite](https://vite.dev/), chúng tôi khuyến nghị dùng framework kiểm thử đơn vị có thể tận dụng trực tiếp cùng cấu hình và pipeline biến đổi của Vite. [Vitest](https://vitest.dev/) là framework kiểm thử đơn vị được thiết kế riêng cho mục đích này, do các thành viên của đội Vue / Vite tạo ra và duy trì. Nó tích hợp với dự án dựa trên Vite với rất ít công sức và có tốc độ cực nhanh.

### Các Lựa Chọn Khác {#other-options}

- [Jest](https://jestjs.io/) là một framework kiểm thử đơn vị phổ biến. Tuy nhiên, chúng tôi chỉ khuyến nghị Jest nếu bạn đã có sẵn test suite bằng Jest cần được chuyển sang dự án dựa trên Vite, vì Vitest cho khả năng tích hợp mượt hơn và hiệu năng tốt hơn.

## Kiểm Thử Component {#component-testing}

Trong ứng dụng Vue, component là khối xây dựng chính của UI. Vì vậy, component là đơn vị cô lập tự nhiên khi cần xác thực hành vi của ứng dụng. Xét về độ hạt, kiểm thử component nằm ở khoảng giữa unit test và integration test. Phần lớn ứng dụng Vue của bạn nên được bao phủ bởi kiểm thử component, và chúng tôi khuyến nghị mỗi component Vue nên có file spec riêng.

Kiểm thử component nên phát hiện được các vấn đề liên quan tới props, events, slots mà component cung cấp, styles, classes, hook vòng đời và nhiều thứ khác.

Kiểm thử component không nên mock component con, mà thay vào đó nên kiểm tra sự tương tác giữa component của bạn với các component con bằng cách tương tác với chúng như người dùng thật. Ví dụ, một bài kiểm thử component nên click vào phần tử như người dùng thật thay vì tương tác với component bằng lập trình.

Kiểm thử component nên tập trung vào giao diện công khai của component thay vì chi tiết triển khai bên trong. Với phần lớn component, giao diện công khai chỉ giới hạn ở: event được emit, props và slots. Khi kiểm thử, hãy nhớ **kiểm tra component làm gì, chứ không phải nó làm bằng cách nào**.

**NÊN**

- Với logic **hiển thị**: khẳng định đầu ra render đúng dựa trên props và slots đầu vào.
- Với logic **hành vi**: khẳng định cập nhật render hoặc event emit đúng để phản hồi lại sự kiện nhập từ người dùng.

  Trong ví dụ dưới đây, ta minh họa một component Stepper có phần tử DOM gắn nhãn "increment" và có thể được click. Ta truyền một prop tên là `max`, prop này ngăn Stepper tăng quá `2`, nên nếu click nút 3 lần thì UI vẫn chỉ nên hiển thị `2`.

  Ta không biết gì về chi tiết triển khai của Stepper, chỉ biết rằng "đầu vào" là prop `max` và "đầu ra" là trạng thái của DOM như người dùng sẽ nhìn thấy.

::: code-group

```js [Vue Test Utils]
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

const wrapper = mount(Stepper, {
  props: {
    max: 1
  }
})

expect(wrapper.find(valueSelector).text()).toContain('0')

await wrapper.find(buttonSelector).trigger('click')

expect(wrapper.find(valueSelector).text()).toContain('1')
```

```js [Cypress]
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

mount(Stepper, {
  props: {
    max: 1
  }
})

cy.get(valueSelector)
  .should('be.visible')
  .and('contain.text', '0')
  .get(buttonSelector)
  .click()
  .get(valueSelector)
  .should('contain.text', '1')
```

```js [Testing Library]
const { getByText } = render(Stepper, {
  props: {
    max: 1
  }
})

getByText('0') // Assertion ngầm rằng "0" nằm trong component

const button = getByRole('button', { name: /increment/i })

// Phát ra sự kiện click cho nút tăng.
await fireEvent.click(button)

getByText('1')

await fireEvent.click(button)
```

:::

**KHÔNG NÊN**

- Đừng khẳng định state riêng tư của instance component hoặc kiểm thử các phương thức riêng tư của component. Kiểm thử chi tiết triển khai sẽ làm test trở nên mong manh, vì chúng dễ hỏng và cần cập nhật hơn mỗi khi phần cài đặt thay đổi.

  Công việc cuối cùng của component là render đúng đầu ra DOM, nên những bài kiểm thử tập trung vào đầu ra DOM sẽ đem lại cùng mức độ đảm bảo về tính đúng đắn (thậm chí còn tốt hơn), đồng thời bền vững và chống chịu thay đổi tốt hơn.

  Đừng chỉ dựa hoàn toàn vào snapshot test. Việc khẳng định chuỗi HTML không mô tả được tính đúng đắn. Hãy viết test có chủ đích rõ ràng.

  Nếu một phương thức cần được kiểm thử kỹ, hãy cân nhắc tách nó thành một hàm utility độc lập và viết unit test riêng cho nó. Nếu không thể tách sạch sẽ, bạn có thể kiểm thử nó như một phần của component test, integration test hoặc end-to-end test có bao phủ logic đó.

### Khuyến Nghị {#recommendation-1}

- [Vitest](https://vitest.dev/) dành cho component hoặc composable render không cần giao diện trình duyệt đầy đủ (ví dụ: hàm [`useFavicon`](https://vueuse.org/core/useFavicon/#usefavicon) trong VueUse). Component và DOM có thể được kiểm thử bằng [`@vue/test-utils`](https://github.com/vuejs/test-utils).

- [Cypress Component Testing](https://on.cypress.io/component) dành cho component mà hành vi kỳ vọng phụ thuộc vào việc render style đúng hoặc kích hoạt sự kiện DOM gốc. Nó có thể được dùng cùng Testing Library thông qua [@testing-library/cypress](https://testing-library.com/docs/cypress-testing-library/intro).

Khác biệt chính giữa Vitest và các runner chạy trong trình duyệt là tốc độ và ngữ cảnh thực thi. Nói ngắn gọn, các runner chạy trong trình duyệt như Cypress có thể bắt được những vấn đề mà runner chạy trong Node như Vitest không bắt được (ví dụ: vấn đề style, sự kiện DOM gốc thật, cookies, local storage và lỗi mạng), nhưng các runner chạy trong trình duyệt lại _chậm hơn Vitest nhiều bậc_ vì chúng phải mở trình duyệt, biên dịch stylesheet và làm thêm nhiều việc khác. Cypress là một runner chạy trong trình duyệt có hỗ trợ kiểm thử component. Hãy đọc [trang so sánh của Vitest](https://vitest.dev/guide/comparisons.html#cypress) để xem thông tin mới nhất khi so sánh Vitest và Cypress.

### Thư Viện Mount {#mounting-libraries}

Kiểm thử component thường bao gồm việc mount component cần kiểm thử một cách độc lập, kích hoạt các sự kiện nhập mô phỏng từ người dùng, rồi khẳng định đầu ra DOM được render. Có những thư viện utility chuyên dụng giúp các tác vụ này trở nên đơn giản hơn.

- [`@vue/test-utils`](https://github.com/vuejs/test-utils) là thư viện kiểm thử component ở mức thấp chính thức, được viết để cung cấp cho người dùng quyền truy cập vào các API đặc thù của Vue. Đây cũng là thư viện ở tầng thấp hơn mà `@testing-library/vue` xây dựng dựa trên.

- [`@testing-library/vue`](https://github.com/testing-library/vue-testing-library) là thư viện kiểm thử Vue tập trung vào việc kiểm thử component mà không phụ thuộc vào chi tiết triển khai. Nguyên tắc dẫn dắt của nó là: test càng giống cách phần mềm được sử dụng ngoài thực tế, nó càng đem lại nhiều sự tự tin.

Chúng tôi khuyến nghị dùng `@vue/test-utils` để kiểm thử component trong ứng dụng. `@testing-library/vue` có vấn đề khi kiểm thử component bất đồng bộ với Suspense, nên cần được dùng một cách thận trọng.

### Các Lựa Chọn Khác {#other-options-1}

- [Nightwatch](https://nightwatchjs.org/) là runner kiểm thử E2E có hỗ trợ Vue Component Testing. ([Dự án ví dụ](https://github.com/nightwatchjs-community/todo-vue))

- [WebdriverIO](https://webdriver.io/docs/component-testing/vue) dành cho kiểm thử component đa trình duyệt dựa trên tương tác người dùng gốc được chuẩn hóa bằng tự động hóa. Nó cũng có thể dùng cùng Testing Library.

## Kiểm Thử E2E {#e2e-testing}

Unit test và component test giúp lập trình viên tự tin hơn, nhưng vẫn có giới hạn trong khả năng bao phủ toàn diện ứng dụng khi triển khai lên production. Đó là lúc kiểm thử end-to-end (E2E) phát huy: nó bao phủ điều quan trọng nhất — điều gì xảy ra khi người dùng thực sự dùng ứng dụng của bạn.

Kiểm thử end-to-end tập trung vào hành vi của ứng dụng qua nhiều trang, có thực hiện request mạng với ứng dụng Vue đã được build cho production. Chúng thường bao gồm việc dựng một cơ sở dữ liệu hoặc backend khác, và thậm chí có thể được chạy trên môi trường staging thật.

Kiểm thử end-to-end thường phát hiện các vấn đề ở router, thư viện quản lý state, component cấp cao nhất (ví dụ: App hoặc Layout), asset công khai hoặc bất kỳ logic xử lý request nào. Như đã nói ở trên, chúng bắt được các vấn đề nghiêm trọng mà unit test hoặc component test có thể không thể phát hiện.

Kiểm thử end-to-end không import mã nào từ ứng dụng Vue của bạn, mà hoàn toàn dựa vào việc kiểm thử ứng dụng bằng cách điều hướng qua các trang thật trong trình duyệt thật.

Kiểm thử end-to-end xác thực nhiều tầng trong ứng dụng của bạn. Chúng có thể nhắm vào ứng dụng được build cục bộ hoặc thậm chí một môi trường staging thật. Việc kiểm thử trên môi trường staging không chỉ bao gồm mã frontend và static server của bạn mà còn cả các dịch vụ backend và hạ tầng đi kèm.

> Test của bạn càng giống với cách phần mềm được sử dụng ngoài thực tế, chúng càng mang lại nhiều sự tự tin. - [Kent C. Dodds](https://x.com/kentcdodds/status/977018512689455106) - Tác giả của Testing Library

Bằng cách kiểm thử tác động của hành động người dùng lên ứng dụng, test E2E thường là chìa khóa để nâng mức độ tự tin xem ứng dụng có đang hoạt động đúng hay không.

### Chọn Giải Pháp Kiểm Thử E2E {#choosing-an-e2e-testing-solution}

Kiểm thử end-to-end (E2E) trên web từng mang tiếng xấu vì test hay bị flaky và làm chậm quá trình phát triển. Nhưng các công cụ E2E hiện đại đã tiến một bước dài: test đáng tin cậy hơn, tương tác và hữu ích hơn nhiều. Khi chọn framework kiểm thử E2E, dưới đây là một số điểm bạn nên lưu ý.

#### Kiểm Thử Đa Trình Duyệt {#cross-browser-testing}

Một trong những lợi ích chính khiến kiểm thử end-to-end (E2E) được biết đến nhiều là khả năng kiểm thử ứng dụng của bạn trên nhiều trình duyệt. Mặc dù nghe có vẻ hấp dẫn khi đạt độ bao phủ đa trình duyệt 100%, điều quan trọng cần lưu ý là kiểm thử đa trình duyệt sẽ đem lại lợi ích giảm dần so với tài nguyên của nhóm do cần thêm thời gian và năng lực máy để chạy ổn định. Vì vậy, bạn cần cân nhắc đánh đổi này khi quyết định mức độ kiểm thử đa trình duyệt mà ứng dụng của bạn thật sự cần.

#### Vòng Phản Hồi Nhanh Hơn {#faster-feedback-loops}

Một trong những vấn đề lớn của kiểm thử end-to-end (E2E) trong quá trình phát triển là chạy toàn bộ suite tốn rất nhiều thời gian. Thông thường điều này chỉ được thực hiện trong pipeline continuous integration và deployment (CI/CD). Các framework kiểm thử E2E hiện đại đã góp phần giải quyết vấn đề này bằng các tính năng như chạy song song, cho phép pipeline CI/CD thường chạy nhanh hơn nhiều lần so với trước đây. Ngoài ra, khi phát triển cục bộ, khả năng chỉ chạy một test cho đúng trang bạn đang làm, đồng thời vẫn có hot reload cho test, có thể cải thiện đáng kể quy trình làm việc và năng suất của lập trình viên.

#### Trải Nghiệm Debug Hạng Nhất {#first-class-debugging-experience}

Trong khi trước đây lập trình viên thường phải dựa vào việc rà log trong cửa sổ terminal để xác định test hỏng ở đâu, các framework kiểm thử end-to-end (E2E) hiện đại cho phép tận dụng những công cụ mà lập trình viên đã quen dùng sẵn, chẳng hạn như browser developer tools.

#### Khả Năng Quan Sát Ở Chế Độ Headless {#visibility-in-headless-mode}

Khi kiểm thử end-to-end (E2E) được chạy trong pipeline continuous integration/deployment, chúng thường chạy trong trình duyệt headless (tức là không mở trình duyệt hiển thị cho người dùng quan sát). Một tính năng quan trọng của các framework kiểm thử E2E hiện đại là khả năng xem ảnh chụp nhanh và/hoặc video của ứng dụng trong quá trình kiểm thử, nhờ đó cung cấp thêm manh mối về nguyên nhân lỗi xảy ra. Trước đây việc duy trì các tích hợp như vậy khá phiền phức.

### Khuyến Nghị {#recommendation-2}

- [Playwright](https://playwright.dev/) là một giải pháp kiểm thử E2E rất tốt, hỗ trợ Chromium, WebKit và Firefox. Bạn có thể kiểm thử trên Windows, Linux và macOS, ở máy cục bộ hoặc trên CI, chạy có giao diện hoặc headless với khả năng giả lập thiết bị di động gốc cho Google Chrome trên Android và Mobile Safari. Nó có giao diện giàu thông tin, khả năng debug xuất sắc, assertion tích hợp sẵn, chạy song song, traces và được thiết kế để giảm thiểu test flaky. Playwright có hỗ trợ [Component Testing](https://playwright.dev/docs/test-components), nhưng vẫn đang ở mức thử nghiệm. Playwright là mã nguồn mở và được Microsoft duy trì.

- [Cypress](https://www.cypress.io/) có giao diện đồ họa giàu thông tin, khả năng debug xuất sắc, assertion tích hợp sẵn, stubs, khả năng chống flaky và snapshots. Như đã đề cập ở trên, nó cung cấp hỗ trợ ổn định cho [Component Testing](https://docs.cypress.io/guides/component-testing/introduction). Cypress hỗ trợ các trình duyệt dựa trên Chromium, Firefox và Electron. Hỗ trợ WebKit có sẵn nhưng vẫn ở mức thử nghiệm. Cypress dùng giấy phép MIT, nhưng một số tính năng như chạy song song yêu cầu đăng ký Cypress Cloud.

<div class="lambdatest">
  <a href="https://lambdatest.com" target="_blank">
    <img src="/images/lambdatest.svg">
    <div>
      <div class="testing-partner">Nhà tài trợ cho phần kiểm thử</div>
      <div>Lambdatest là nền tảng đám mây để chạy kiểm thử E2E, khả năng tiếp cận và hồi quy thị giác trên mọi trình duyệt phổ biến cùng thiết bị thật, với khả năng sinh test có AI hỗ trợ.</div>
    </div>
  </a>
</div>

### Các Lựa Chọn Khác {#other-options-2}

- [Nightwatch](https://nightwatchjs.org/) là một giải pháp kiểm thử E2E dựa trên [Selenium WebDriver](https://www.npmjs.com/package/selenium-webdriver). Nhờ đó, nó có phạm vi hỗ trợ trình duyệt rộng nhất, bao gồm cả kiểm thử di động gốc. Các giải pháp dựa trên Selenium sẽ chậm hơn Playwright hoặc Cypress.

- [WebdriverIO](https://webdriver.io/) là framework tự động hóa kiểm thử cho web và mobile, dựa trên giao thức WebDriver.

## Công Thức Mẫu {#recipes}

### Thêm Vitest Vào Dự Án {#adding-vitest-to-a-project}

Trong một dự án Vue dựa trên Vite, hãy chạy:

```sh
> npm install -D vitest happy-dom @testing-library/vue
```

Tiếp theo, cập nhật cấu hình Vite để thêm khối tùy chọn `test`:

```js{5-11} [vite.config.js]
import { defineConfig } from 'vite'

export default defineConfig({
  // ...
  test: {
    // bật các global test API theo kiểu jest
    globals: true,
    // mô phỏng DOM bằng happy-dom
    // (cần cài happy-dom như một peer dependency)
    environment: 'happy-dom'
  }
})
```

:::tip
Nếu bạn dùng TypeScript, hãy thêm `vitest/globals` vào trường `types` trong `tsconfig.json`.

```json [tsconfig.json]
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

:::

Sau đó, tạo một file có hậu tố `*.test.js` trong dự án. Bạn có thể đặt toàn bộ file test trong một thư mục test ở gốc dự án hoặc đặt trong các thư mục test nằm cạnh file nguồn. Vitest sẽ tự động tìm chúng dựa trên quy ước đặt tên này.

```js [MyComponent.test.js]
import { render } from '@testing-library/vue'
import MyComponent from './MyComponent.vue'

test('it should work', () => {
  const { getByText } = render(MyComponent, {
    props: {
      /* ... */
    }
  })

  // khẳng định đầu ra
  getByText('...')
})
```

Cuối cùng, cập nhật `package.json` để thêm script test rồi chạy nó:

```json{4} [package.json]
{
  // ...
  "scripts": {
    "test": "vitest"
  }
}
```

```sh
> npm test
```

### Kiểm Thử Composable {#testing-composables}

> Phần này giả định rằng bạn đã đọc mục [Composable](/guide/reusability/composables).

Khi nói tới việc kiểm thử composable, ta có thể chia chúng thành hai nhóm: composable không phụ thuộc vào instance component chủ, và composable có phụ thuộc.

Một composable phụ thuộc vào instance component chủ khi nó dùng các API sau:

- Hook vòng đời
- Provide / Inject

Nếu composable chỉ dùng Reactivity API, bạn có thể kiểm thử nó bằng cách gọi trực tiếp và khẳng định state / methods mà nó trả về:

```js [counter.js]
import { ref } from 'vue'

export function useCounter() {
  const count = ref(0)
  const increment = () => count.value++

  return {
    count,
    increment
  }
}
```

```js [counter.test.js]
import { useCounter } from './counter.js'

test('useCounter', () => {
  const { count, increment } = useCounter()
  expect(count.value).toBe(0)

  increment()
  expect(count.value).toBe(1)
})
```

Một composable phụ thuộc vào hook vòng đời hoặc Provide / Inject cần được bọc trong một component chủ để có thể kiểm thử. Ta có thể tạo một helper như sau:

```js [test-utils.js]
import { createApp } from 'vue'

export function withSetup(composable) {
  let result
  const app = createApp({
    setup() {
      result = composable()
      // chặn cảnh báo thiếu template
      return () => {}
    }
  })
  app.mount(document.createElement('div'))
  // trả về kết quả và instance ứng dụng
  // để kiểm thử provide/unmount
  return [result, app]
}
```

```js [foo.test.js]
import { withSetup } from './test-utils'
import { useFoo } from './foo'

test('useFoo', () => {
  const [result, app] = withSetup(() => useFoo(123))
  // mock provide để kiểm thử injection
  app.provide(...)
  // chạy các assertion
  expect(result.foo.value).toBe(1)
  // kích hoạt hook onUnmounted nếu cần
  app.unmount()
})
```

Với composable phức tạp hơn, đôi khi sẽ dễ hơn nếu kiểm thử nó bằng cách viết test cho component bao ngoài, dùng kỹ thuật [Kiểm Thử Component](#component-testing).

<!--
TODO more testing recipes can be added in the future e.g.
- How to set up CI via GitHub actions
- How to do mocking in component testing
-->
