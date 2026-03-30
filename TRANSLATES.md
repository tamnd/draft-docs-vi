# Translation Checklist

Tài liệu này dùng để theo dõi tiến độ dịch thủ công cho repo `docs-vi`.

## Trạng thái

- `[ ]` chưa bắt đầu
- `[x]` đã dịch và đã review
- Khi đang làm dở, thêm hậu tố `(WIP)` vào cuối dòng checklist

## Workflow

1. Mỗi trang nên đi qua issue hoặc PR riêng để dễ review.
2. Không dịch hàng loạt rồi merge một lần lớn.
3. Bám theo cấu trúc và liên kết của `vuejs/docs`.
4. Khi upstream thay đổi, đồng bộ trước rồi cập nhật bản dịch theo diff.

## Quy ước dịch

- Giữ nguyên anchor gốc: `## Tiêu đề đã dịch {#original-anchor}`.
- Giữ nguyên marker của Vue docs, ví dụ `### <script setup> ** {#script-setup}`.
- Ưu tiên văn phong kỹ thuật rõ ràng, ngắn, dễ đọc.
- Tên API, directive, component, package, và code sample giữ nguyên tiếng Anh.
- Thuật ngữ cần được thống nhất trong [GLOSSARY.md](./GLOSSARY.md) trước khi dùng rộng rãi.
- Text ngoài nội dung markdown cũng cần dịch, đặc biệt là `.vitepress/config.ts`.
- Ảnh có chữ cần xử lý riêng; tạo file ảnh mới thay vì ghi đè asset gốc nếu cần biến thể tiếng Việt.

## Checklist 30 trang tiếp theo

- [ ] `src/guide/scaling-up/sfc.md`
- [ ] `src/guide/scaling-up/ssr.md`
- [ ] `src/guide/scaling-up/state-management.md`
- [ ] `src/guide/scaling-up/testing.md`
- [ ] `src/guide/scaling-up/tooling.md`
- [ ] `src/guide/typescript/composition-api.md`
- [ ] `src/guide/typescript/options-api.md`
- [ ] `src/guide/typescript/overview.md`
- [ ] `src/api/application.md`
- [ ] `src/api/built-in-components.md`
- [ ] `src/api/built-in-directives.md`
- [ ] `src/api/built-in-special-attributes.md`
- [ ] `src/api/built-in-special-elements.md`
- [ ] `src/api/compile-time-flags.md`
- [ ] `src/api/component-instance.md`
- [ ] `src/api/composition-api-dependency-injection.md`
- [ ] `src/api/composition-api-helpers.md`
- [ ] `src/api/composition-api-lifecycle.md`
- [ ] `src/api/composition-api-setup.md`
- [ ] `src/api/custom-elements.md`
- [ ] `src/api/custom-renderer.md`
- [ ] `src/api/general.md`
- [ ] `src/api/index.md`
- [ ] `src/api/options-composition.md`
- [ ] `src/api/options-lifecycle.md`
- [ ] `src/api/options-misc.md`
- [ ] `src/api/options-rendering.md`
- [ ] `src/api/options-state.md`
- [ ] `src/api/reactivity-advanced.md`
- [ ] `src/api/reactivity-core.md`

## Ưu tiên sprint đầu

- [ ] `README.md`
- [ ] `TRANSLATES.md`
- [ ] `.vitepress/config.ts`
- [ ] `src/index.md`
- [ ] `src/guide/introduction.md`
- [ ] `src/guide/quick-start.md`
- [x] `src/guide/essentials/application.md`
- [x] `src/guide/essentials/template-syntax.md`
- [x] `src/guide/essentials/class-and-style.md`
- [x] `src/guide/essentials/component-basics.md`
- [x] `src/guide/essentials/computed.md`
- [x] `src/guide/essentials/conditional.md`
- [x] `src/guide/essentials/event-handling.md`
- [x] `src/guide/essentials/forms.md`
- [x] `src/guide/essentials/lifecycle.md`
- [x] `src/guide/essentials/list.md`
- [x] `src/guide/essentials/reactivity-fundamentals.md`
- [x] `src/guide/essentials/template-refs.md`

## Repo meta

- [ ] `README.md`
- [ ] `TRANSLATES.md`
- [ ] `.vitepress/config.ts`

## Landing and shared pages

- [ ] `src/index.md`
- [ ] `src/translations/index.md`
- [ ] `src/glossary/index.md`
- [ ] `src/error-reference/index.md`
- [ ] `src/examples/index.md`

## Guide

- [ ] `src/guide/best-practices/accessibility.md`
- [ ] `src/guide/best-practices/performance.md`
- [ ] `src/guide/best-practices/production-deployment.md`
- [ ] `src/guide/best-practices/security.md`
- [ ] `src/guide/built-ins/keep-alive.md`
- [ ] `src/guide/built-ins/suspense.md`
- [ ] `src/guide/built-ins/teleport.md`
- [ ] `src/guide/built-ins/transition-group.md`
- [ ] `src/guide/built-ins/transition.md`
- [x] `src/guide/components/async.md`
- [x] `src/guide/components/attrs.md`
- [x] `src/guide/components/events.md`
- [x] `src/guide/components/props.md`
- [x] `src/guide/components/provide-inject.md`
- [x] `src/guide/components/registration.md`
- [x] `src/guide/components/slots.md`
- [x] `src/guide/components/v-model.md`
- [x] `src/guide/essentials/application.md`
- [x] `src/guide/essentials/class-and-style.md`
- [x] `src/guide/essentials/component-basics.md`
- [x] `src/guide/essentials/computed.md`
- [x] `src/guide/essentials/conditional.md`
- [x] `src/guide/essentials/event-handling.md`
- [x] `src/guide/essentials/forms.md`
- [x] `src/guide/essentials/lifecycle.md`
- [x] `src/guide/essentials/list.md`
- [x] `src/guide/essentials/reactivity-fundamentals.md`
- [x] `src/guide/essentials/template-refs.md`
- [x] `src/guide/essentials/template-syntax.md`
- [x] `src/guide/essentials/watchers.md`
- [ ] `src/guide/extras/animation.md`
- [ ] `src/guide/extras/composition-api-faq.md`
- [ ] `src/guide/extras/reactivity-in-depth.md`
- [ ] `src/guide/extras/reactivity-transform.md`
- [ ] `src/guide/extras/render-function.md`
- [ ] `src/guide/extras/rendering-mechanism.md`
- [ ] `src/guide/extras/ways-of-using-vue.md`
- [ ] `src/guide/extras/web-components.md`
- [ ] `src/guide/introduction.md`
- [ ] `src/guide/quick-start.md`
- [ ] `src/guide/reusability/composables.md`
- [ ] `src/guide/reusability/custom-directives.md`
- [ ] `src/guide/reusability/plugins.md`
- [ ] `src/guide/scaling-up/routing.md`
- [ ] `src/guide/scaling-up/sfc.md`
- [ ] `src/guide/scaling-up/ssr.md`
- [ ] `src/guide/scaling-up/state-management.md`
- [ ] `src/guide/scaling-up/testing.md`
- [ ] `src/guide/scaling-up/tooling.md`
- [ ] `src/guide/typescript/composition-api.md`
- [ ] `src/guide/typescript/options-api.md`
- [ ] `src/guide/typescript/overview.md`

## API

- [ ] `src/api/application.md`
- [ ] `src/api/built-in-components.md`
- [ ] `src/api/built-in-directives.md`
- [ ] `src/api/built-in-special-attributes.md`
- [ ] `src/api/built-in-special-elements.md`
- [ ] `src/api/compile-time-flags.md`
- [ ] `src/api/component-instance.md`
- [ ] `src/api/composition-api-dependency-injection.md`
- [ ] `src/api/composition-api-helpers.md`
- [ ] `src/api/composition-api-lifecycle.md`
- [ ] `src/api/composition-api-setup.md`
- [ ] `src/api/custom-elements.md`
- [ ] `src/api/custom-renderer.md`
- [ ] `src/api/general.md`
- [ ] `src/api/index.md`
- [ ] `src/api/options-composition.md`
- [ ] `src/api/options-lifecycle.md`
- [ ] `src/api/options-misc.md`
- [ ] `src/api/options-rendering.md`
- [ ] `src/api/options-state.md`
- [ ] `src/api/reactivity-advanced.md`
- [ ] `src/api/reactivity-core.md`
- [ ] `src/api/reactivity-utilities.md`
- [ ] `src/api/render-function.md`
- [ ] `src/api/sfc-css-features.md`
- [ ] `src/api/sfc-script-setup.md`
- [ ] `src/api/sfc-spec.md`
- [ ] `src/api/ssr.md`
- [ ] `src/api/utility-types.md`

## Tutorial

- [ ] `src/tutorial/index.md`
- [ ] `src/tutorial/src/step-1/description.md`
- [ ] `src/tutorial/src/step-10/description.md`
- [ ] `src/tutorial/src/step-11/description.md`
- [ ] `src/tutorial/src/step-12/description.md`
- [ ] `src/tutorial/src/step-13/description.md`
- [ ] `src/tutorial/src/step-14/description.md`
- [ ] `src/tutorial/src/step-15/description.md`
- [ ] `src/tutorial/src/step-2/description.md`
- [ ] `src/tutorial/src/step-3/description.md`
- [ ] `src/tutorial/src/step-4/description.md`
- [ ] `src/tutorial/src/step-5/description.md`
- [ ] `src/tutorial/src/step-6/description.md`
- [ ] `src/tutorial/src/step-7/description.md`
- [ ] `src/tutorial/src/step-8/description.md`
- [ ] `src/tutorial/src/step-9/description.md`

## About

- [ ] `src/about/coc.md`
- [ ] `src/about/community-guide.md`
- [ ] `src/about/faq.md`
- [ ] `src/about/privacy.md`
- [ ] `src/about/releases.md`
- [ ] `src/about/team.md`

## Ecosystem

- [ ] `src/ecosystem/newsletters.md`
- [ ] `src/ecosystem/themes.md`

## Style guide

- [ ] `src/style-guide/index.md`
- [ ] `src/style-guide/rules-essential.md`
- [ ] `src/style-guide/rules-recommended.md`
- [ ] `src/style-guide/rules-strongly-recommended.md`
- [ ] `src/style-guide/rules-use-with-caution.md`

## Sponsor and partners

- [ ] `src/sponsor/index.md`
- [ ] `src/partners/[partnerId].md`
- [ ] `src/partners/all.md`
- [ ] `src/partners/index.md`

## Examples descriptions

- [ ] `src/examples/src/attribute-bindings/description.txt`
- [ ] `src/examples/src/cells/description.txt`
- [ ] `src/examples/src/circle-drawer/description.txt`
- [ ] `src/examples/src/conditionals-and-loops/description.txt`
- [ ] `src/examples/src/counter/description.txt`
- [ ] `src/examples/src/crud/description.txt`
- [ ] `src/examples/src/fetching-data/description.txt`
- [ ] `src/examples/src/flight-booker/description.txt`
- [ ] `src/examples/src/form-bindings/description.txt`
- [ ] `src/examples/src/grid/description.txt`
- [ ] `src/examples/src/handling-input/description.txt`
- [ ] `src/examples/src/hello-world/description.txt`
- [ ] `src/examples/src/list-transition/description.txt`
- [ ] `src/examples/src/markdown/description.txt`
- [ ] `src/examples/src/modal/description.txt`
- [ ] `src/examples/src/simple-component/description.txt`
- [ ] `src/examples/src/svg/description.txt`
- [ ] `src/examples/src/temperature-converter/description.txt`
- [ ] `src/examples/src/timer/description.txt`
- [ ] `src/examples/src/tree/description.txt`
