# Translation Roadmap

Step-by-step translation plan for [`vuejs-translations/docs-vi`](https://github.com/vuejs-translations/docs-vi), the official Vietnamese translation of the Vue.js documentation.

## Site Shell

> Get the site running in Vietnamese with correct navigation and UI labels.
> ~830 lines, ~1,800 words

- [x] `README.md` - repo introduction, local setup instructions, contribution guide
- [x] `.vitepress/config.ts` - set `lang`, translate all nav, sidebar, and i18n strings
- [x] `src/index.md` - homepage (hero, tagline, CTA buttons)
- [x] `CHECKPOINT.md` - record the upstream commit hash at the start of translation

## Introduction and Quick Start

> The first pages every new user reads.
> ~700 lines, ~3,300 words

- [x] `src/guide/introduction.md`
- [x] `src/guide/quick-start.md`

## Essentials

> 13 pages covering the core of Vue. 
> ~4,870 lines, ~20,000 words

- [ ] `src/guide/essentials/application.md`
- [ ] `src/guide/essentials/template-syntax.md`
- [ ] `src/guide/essentials/reactivity-fundamentals.md`
- [ ] `src/guide/essentials/computed.md`
- [ ] `src/guide/essentials/class-and-style.md`
- [ ] `src/guide/essentials/conditional.md`
- [ ] `src/guide/essentials/list.md`
- [ ] `src/guide/essentials/event-handling.md`
- [ ] `src/guide/essentials/forms.md`
- [ ] `src/guide/essentials/lifecycle.md`
- [ ] `src/guide/essentials/template-refs.md`
- [ ] `src/guide/essentials/watchers.md`
- [ ] `src/guide/essentials/component-basics.md`

## Components In-Depth

> 8 pages covering Vue's component model.
> ~3,140 lines, ~11,900 words

- [ ] `src/guide/components/registration.md`
- [ ] `src/guide/components/props.md`
- [ ] `src/guide/components/events.md`
- [ ] `src/guide/components/v-model.md`
- [ ] `src/guide/components/attrs.md`
- [ ] `src/guide/components/slots.md`
- [ ] `src/guide/components/provide-inject.md`
- [ ] `src/guide/components/async.md`

## Reusability and Built-ins

> Composables, custom directives, plugins, and built-in components.
> ~2,135 lines, ~10,300 words

- [ ] `src/guide/reusability/composables.md`
- [ ] `src/guide/reusability/custom-directives.md`
- [ ] `src/guide/reusability/plugins.md`
- [ ] `src/guide/built-ins/transition.md`
- [ ] `src/guide/built-ins/transition-group.md`
- [ ] `src/guide/built-ins/keep-alive.md`
- [ ] `src/guide/built-ins/teleport.md`
- [ ] `src/guide/built-ins/suspense.md`

## Scaling Up

> Routing, state management, testing, tooling, SSR, and TypeScript.
> ~2,480 lines, ~14,500 words

- [ ] `src/guide/scaling-up/sfc.md`
- [ ] `src/guide/scaling-up/routing.md`
- [ ] `src/guide/scaling-up/state-management.md`
- [ ] `src/guide/scaling-up/testing.md`
- [ ] `src/guide/scaling-up/tooling.md`
- [ ] `src/guide/scaling-up/ssr.md`
- [ ] `src/guide/typescript/overview.md`
- [ ] `src/guide/typescript/composition-api.md`
- [ ] `src/guide/typescript/options-api.md`

## Advanced and Best Practices

> Deeper content, best tackled after Essentials through Scaling Up are complete.
> ~3,860 lines, ~21,400 words

- [ ] `src/guide/extras/composition-api-faq.md`
- [ ] `src/guide/extras/reactivity-in-depth.md`
- [ ] `src/guide/extras/rendering-mechanism.md`
- [ ] `src/guide/extras/render-function.md`
- [ ] `src/guide/extras/animation.md`
- [ ] `src/guide/extras/web-components.md`
- [ ] `src/guide/extras/ways-of-using-vue.md`
- [ ] `src/guide/extras/reactivity-transform.md`
- [ ] `src/guide/best-practices/accessibility.md`
- [ ] `src/guide/best-practices/performance.md`
- [ ] `src/guide/best-practices/production-deployment.md`
- [ ] `src/guide/best-practices/security.md`

## API Reference

> 29 pages plus the ApiIndex component.
> ~7,965 lines, ~30,900 words

- [ ] `src/api/application.md`
- [ ] `src/api/general.md`
- [ ] `src/api/composition-api-setup.md`
- [ ] `src/api/composition-api-lifecycle.md`
- [ ] `src/api/composition-api-helpers.md`
- [ ] `src/api/composition-api-dependency-injection.md`
- [ ] `src/api/reactivity-core.md`
- [ ] `src/api/reactivity-utilities.md`
- [ ] `src/api/reactivity-advanced.md`
- [ ] `src/api/options-state.md`
- [ ] `src/api/options-lifecycle.md`
- [ ] `src/api/options-composition.md`
- [ ] `src/api/options-rendering.md`
- [ ] `src/api/options-misc.md`
- [ ] `src/api/component-instance.md`
- [ ] `src/api/built-in-directives.md`
- [ ] `src/api/built-in-components.md`
- [ ] `src/api/built-in-special-elements.md`
- [ ] `src/api/built-in-special-attributes.md`
- [ ] `src/api/sfc-spec.md`
- [ ] `src/api/sfc-script-setup.md`
- [ ] `src/api/sfc-css-features.md`
- [ ] `src/api/render-function.md`
- [ ] `src/api/ssr.md`
- [ ] `src/api/utility-types.md`
- [ ] `src/api/custom-elements.md`
- [ ] `src/api/custom-renderer.md`
- [ ] `src/api/compile-time-flags.md`
- [ ] `src/api/index.md`
- [ ] `src/api/ApiIndex.vue` - translate UI strings: "Filter" label, "Enter keyword" placeholder

## Tutorial and Style Guide

> ~3,195 lines, ~10,100 words

- [ ] `src/tutorial/index.md`
- [ ] `src/tutorial/src/step-1/description.md`
- [ ] `src/tutorial/src/step-2/description.md`
- [ ] `src/tutorial/src/step-3/description.md`
- [ ] `src/tutorial/src/step-4/description.md`
- [ ] `src/tutorial/src/step-5/description.md`
- [ ] `src/tutorial/src/step-6/description.md`
- [ ] `src/tutorial/src/step-7/description.md`
- [ ] `src/tutorial/src/step-8/description.md`
- [ ] `src/tutorial/src/step-9/description.md`
- [ ] `src/tutorial/src/step-10/description.md`
- [ ] `src/tutorial/src/step-11/description.md`
- [ ] `src/tutorial/src/step-12/description.md`
- [ ] `src/tutorial/src/step-13/description.md`
- [ ] `src/tutorial/src/step-14/description.md`
- [ ] `src/tutorial/src/step-15/description.md`
- [ ] `src/tutorial/TutorialRepl.vue` - translate UI strings: "Show me!", "Reset", "Prev", "Next"
- [ ] `src/style-guide/index.md`
- [ ] `src/style-guide/rules-essential.md`
- [ ] `src/style-guide/rules-strongly-recommended.md`
- [ ] `src/style-guide/rules-recommended.md`
- [ ] `src/style-guide/rules-use-with-caution.md`

## Examples

> ~32 lines, ~220 words

- [ ] `src/examples/index.md`
- [ ] `src/examples/src/hello-world/description.txt`
- [ ] `src/examples/src/handling-input/description.txt`
- [ ] `src/examples/src/attribute-bindings/description.txt`
- [ ] `src/examples/src/conditionals-and-loops/description.txt`
- [ ] `src/examples/src/form-bindings/description.txt`
- [ ] `src/examples/src/simple-component/description.txt`
- [ ] `src/examples/src/fetching-data/description.txt`
- [ ] `src/examples/src/list-transition/description.txt`
- [ ] `src/examples/src/svg/description.txt`
- [ ] `src/examples/src/modal/description.txt`
- [ ] `src/examples/src/grid/description.txt`
- [ ] `src/examples/src/tree/description.txt`
- [ ] `src/examples/src/markdown/description.txt`
- [ ] `src/examples/src/counter/description.txt`
- [ ] `src/examples/src/cells/description.txt`
- [ ] `src/examples/src/temperature-converter/description.txt`
- [ ] `src/examples/src/flight-booker/description.txt`
- [ ] `src/examples/src/circle-drawer/description.txt`
- [ ] `src/examples/src/timer/description.txt`
- [ ] `src/examples/src/crud/description.txt`

## Remaining Pages

> Lower priority but needed before official submission.
> ~1,075 lines, ~9,150 words

- [ ] `src/about/faq.md`
- [ ] `src/about/releases.md`
- [ ] `src/about/team.md`
- [ ] `src/about/coc.md`
- [ ] `src/about/community-guide.md`
- [ ] `src/about/privacy.md`
- [ ] `src/translations/index.md`
- [ ] `src/glossary/index.md`
- [ ] `src/error-reference/index.md`
- [ ] `src/error-reference/ErrorsTable.vue` - translate column headers: "Code", "Message"
- [ ] `src/ecosystem/newsletters.md`
- [ ] `src/ecosystem/themes.md`
- [ ] `src/sponsor/index.md`
- [ ] `src/partners/index.md`
- [ ] `src/partners/all.md`
- [ ] `src/partners/[partnerId].md`
- [ ] `src/partners/components/PartnerJoin.vue` - translate 3 UI strings (title, description, link text)
- [ ] `src/partners/components/PartnerLanding.vue` - translate 3 UI strings (spotlight title, featured title, browse link)

## Launch

- [ ] Final review of `GLOSSARY.md` for consistency
- [ ] Verify build passes (`pnpm build`)
- [ ] Configure deployment (Netlify/Vercel) for `vi.vuejs.org`
- [ ] Open PR on `vuejs.org` to add Vietnamese to the language list
- [ ] Announce on Vue Discord and the Vietnamese Vue community

## Syncing with upstream

When `vuejs/docs` has new changes:

1. Check the diff against the current checkpoint:
   ```bash
   cd ../docs
   git fetch origin
   git diff 6313f817..origin/main -- src/
   ```
2. Identify which translated pages are affected
3. Open a PR to update those translations
4. Update the commit hash in `CHECKPOINT.md`

## References

- Draft translation (6 review rounds): [`tamnd/draft-docs-vi`](https://github.com/tamnd/draft-docs-vi)
- Upstream English source: [`vuejs/docs`](https://github.com/vuejs/docs)
- Vue 2 Vietnamese translation: [vi.vuejs.org/v2/guide/](https://vi.vuejs.org/v2/guide/)
- Other translations for reference: [docs-zh-cn](https://github.com/vuejs-translations/docs-zh-cn), [docs-ja](https://github.com/vuejs-translations/docs-ja), [docs-fr](https://github.com/vuejs-translations/docs-fr)
- Terminology glossary: [`GLOSSARY.md`](./GLOSSARY.md)
