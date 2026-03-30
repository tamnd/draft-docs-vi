---
sidebar: false
ads: false
editLink: false
sponsors: false
---

<script setup>
import SponsorsGroup from '@theme/components/SponsorsGroup.vue'
import { load, data } from '@theme/components/sponsors'
import { onMounted } from 'vue'

onMounted(load)
</script>

# Trở thành Nhà tài trợ của Vue.js {#become-a-vue-js-sponsor}

Vue.js là dự án mã nguồn mở được cấp phép MIT và hoàn toàn miễn phí.
Lượng công sức khổng lồ cần thiết để duy trì một hệ sinh thái lớn như vậy và phát triển các tính năng mới cho dự án chỉ có thể bền vững nhờ sự hỗ trợ tài chính hào phóng từ các nhà tài trợ.

## Cách Tài trợ {#how-to-sponsor}

Có thể tài trợ qua [GitHub Sponsors](https://github.com/sponsors/yyx990803) hoặc [OpenCollective](https://opencollective.com/vuejs). Hóa đơn có thể lấy qua hệ thống thanh toán của GitHub. Cả tài trợ định kỳ hàng tháng lẫn đóng góp một lần đều được chấp nhận. Tài trợ định kỳ được quyền đặt logo như quy định trong [Các gói tài trợ](#tier-benefits).

Nếu có câu hỏi về gói tài trợ, quy trình thanh toán hoặc dữ liệu hiển thị nhà tài trợ, vui lòng liên hệ [sponsor@vuejs.org](mailto:sponsor@vuejs.org?subject=Vue.js%20sponsorship%20inquiry).

## Tài trợ Vue với Tư cách Doanh nghiệp {#sponsoring-vue-as-a-business}

Tài trợ Vue mang lại cho bạn sự tiếp cận tuyệt vời với hơn **2 triệu** developer Vue trên toàn thế giới thông qua website và README của các dự án GitHub. Điều này không chỉ trực tiếp tạo ra khách hàng tiềm năng, mà còn cải thiện nhận diện thương hiệu của bạn là một doanh nghiệp quan tâm đến Open Source. Đây là tài sản vô hình nhưng cực kỳ quan trọng đối với các công ty xây dựng sản phẩm cho developer, vì nó cải thiện tỷ lệ chuyển đổi.

Nếu bạn đang dùng Vue để xây dựng sản phẩm tạo doanh thu, việc tài trợ cho phát triển Vue là điều hợp lý về mặt kinh doanh: **nó đảm bảo dự án mà sản phẩm của bạn phụ thuộc vẫn khỏe mạnh và được duy trì tích cực.** Sự hiện diện và hình ảnh thương hiệu tích cực trong cộng đồng Vue cũng giúp dễ dàng thu hút và tuyển dụng developer Vue hơn.

Nếu bạn đang xây dựng sản phẩm mà khách hàng mục tiêu là developer, bạn sẽ nhận được lưu lượng chất lượng cao qua sự hiện diện tài trợ, vì toàn bộ khách truy cập của chúng tôi là developer. Tài trợ cũng xây dựng nhận diện thương hiệu và cải thiện chuyển đổi.

## Tài trợ Vue với Tư cách Cá nhân {#sponsoring-vue-as-an-individual}

Nếu bạn là người dùng cá nhân và thấy Vue tăng năng suất làm việc của mình, hãy cân nhắc đóng góp như một cách bày tỏ sự trân trọng - ví dụ như mua cho chúng tôi một tách cà phê thỉnh thoảng. Nhiều thành viên trong nhóm chúng tôi nhận tài trợ và đóng góp qua GitHub Sponsors. Tìm nút "Sponsor" trên trang cá nhân của từng thành viên trong [trang nhóm](/about/team) của chúng tôi.

Bạn cũng có thể thuyết phục công ty tài trợ Vue với tư cách doanh nghiệp. Điều này có thể không dễ, nhưng tài trợ doanh nghiệp thường có tác động lớn hơn nhiều đến sự bền vững của các dự án OSS so với đóng góp cá nhân, vì vậy bạn sẽ giúp chúng tôi nhiều hơn nếu thành công.

## Quyền lợi Gói Tài trợ {#tier-benefits}

- **Nhà tài trợ Đặc biệt Toàn cầu**:
  - Giới hạn **một** nhà tài trợ trên toàn cầu. <span v-if="!data?.special">Hiện đang trống. [Liên hệ](mailto:sponsor@vuejs.org?subject=Vue.js%20special%20sponsor%20inquiry)!</span><span v-else>(Đã có người)</span>
  - (Độc quyền) Logo **above the fold** trên trang chủ của [vuejs.org](/).
  - (Độc quyền) Shoutout đặc biệt và retweet thường xuyên các lần ra mắt sản phẩm lớn qua [tài khoản X chính thức của Vue](https://x.com/vuejs) (320k follower).
  - Vị trí logo nổi bật nhất ở tất cả vị trí trong các gói bên dưới.
- **Platinum (USD$2,000/tháng)**:
  - Logo nổi bật trên trang chủ của [vuejs.org](/).
  - Logo nổi bật trong sidebar của tất cả các trang nội dung.
  - Logo nổi bật trong README của [`vuejs/core`](https://github.com/vuejs/core) và [`vuejs/vue`](https://github.com/vuejs/core).
- **Gold (USD$500/tháng)**:
  - Logo lớn trên trang chủ của [vuejs.org](/).
  - Logo lớn trong README của `vuejs/core` và `vuejs/vue`.
- **Silver (USD$250/tháng)**:
  - Logo cỡ trung trong file `BACKERS.md` của `vuejs/core` và `vuejs/vue`.
- **Bronze (USD$100/tháng)**:
  - Logo nhỏ trong file `BACKERS.md` của `vuejs/core` và `vuejs/vue`.
- **Generous Backer (USD$50/tháng)**:
  - Tên được liệt kê trong file `BACKERS.md` của `vuejs/core` và `vuejs/vue`, trên các backer cá nhân khác.
- **Individual Backer (USD$5/tháng)**:
  - Tên được liệt kê trong file `BACKERS.md` của `vuejs/core` và `vuejs/vue`.

## Nhà tài trợ Hiện tại {#current-sponsors}

### Nhà tài trợ Đặc biệt Toàn cầu {#special-global-sponsor}

<SponsorsGroup tier="special" placement="page" />

### Platinum {#platinum}

<SponsorsGroup tier="platinum" placement="page" />

### Platinum (Trung Quốc) {#platinum-china}

<SponsorsGroup tier="platinum_china" placement="page" />

### Gold {#gold}

<SponsorsGroup tier="gold" placement="page" />

### Silver {#silver}

<SponsorsGroup tier="silver" placement="page" />
