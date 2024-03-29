[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![License][license-src]][license-href]
[![Nuxt][nuxt-src]][nuxt-href]
[![Volta][volta-src]][volta-href]

# Nuxt Scripts

Better Privacy, Performance, and DX for Third-Party Scripts in Nuxt Apps.

- [👾 &nbsp;Playground](https://stackblitz.com/github/nuxt/scripts/tree/main/playground)

## Features

- 🪨 [useScript](https://unhead.unjs.io/usage/composables/use-script)
  - 🦥 Improve your site performance with better script loading strategies
  - 🎃 Powerful proxy API for SSR handling, lazy loading, and error handling
- (TODO) Registry for third-party scripts in Nuxt
- ⏬ Serve scripts from your own server
- 🕵️ Privacy Features - Trigger scripts loading on consent.
- 🪵 DevTools integration - View your script with their status and see function logs

## Background

Loading third-party IIFE scripts using `useHead` composable is easy. However,
things start getting more complicated quickly around SSR, lazy loading, and type safety.

Nuxt Scripts was created to solve these issues and more with the goal of making third-party scripts more performant,
have better privacy and be better DX overall.

## Quick Start

To get started, simply run:

```bash
npx nuxi@latest module add @nuxt/scripts
```

To start using Nuxt Scripts, you can use the [useScript](https://unhead.unjs.io/usage/composables/use-script) composable to load your third-party scripts.

### Confetti Example

If you want to get a feel for how the module works, you can load the `js-confetti` library:

```ts
interface JSConfettiApi { addConfetti: (options?: { emojis: string[] }) => void }
const { addConfetti } = useScript<JSConfettiApi>('https://cdn.jsdelivr.net/npm/js-confetti@latest/dist/js-confetti.browser.js', {
  trigger: 'onNuxtReady', // load on onNuxtReady
  assetStrategy: 'bundle', // script will be served from your server instead of cdn.jsdelivr.net
  use() {
    return new window.JSConfetti()
  },
})
// useScript is non-blocking, this will run once the script loads
addConfetti({ emojis: ['🌈', '⚡️', '💥', '✨', '💫', '🌸'] })
```

## Guides

### Using The Registry (TODO)

The registry is a collection of composables and Nuxt Modules that directly integrate with Nuxt Scripts.

To use a script from the registry, simply use the composable. Consult the
below table for the available scripts.

| Key      | Description                                                | Composable | Source |
|----------|------------------------------------------------------------| --- | --- |
| `cloudflare-web-analytics` | Cloudflare Web Analytics                                   | `useScriptCloudflareWebAnalytics` | Core |
| `confetti` | [JS Confetti](https://github.com/loonywizard/js-confetti)  | `useScriptCloudflareAnalytics` | Core |
| `facebook-pixel` | Facebook Pixel                                             | `useScriptFacebookPixel` | Core |
| `fathom-analytics` | Fathom Analytics                                           | `useScriptFathomAnalytics` | Core |
| `hotjar` | Hotjar                                                     | `useScriptHotjar` | Core |
| `intercom` | Intercom                                                   | `useScriptIntercom` | Core |
| `segment` | Segment                                                    | `useScriptSegment` | Core |
| `google-analytics` | Google Analytics                                           | `useScriptGoogleAnalytics` | [Nuxt Third Party Capital](https://github.com/nuxt/third-party-capital) |
| `google-tag-manager` | Google Tag Manager                                         | `useScriptGoogleTagManager` | [Nuxt Third Party Capital](https://github.com/nuxt/third-party-capital) |
| `google-maps` | Google Maps                                                | `useScriptGoogleMaps` | [Nuxt Third Party Capital](https://github.com/nuxt/third-party-capital) |
| `cloudflare-turnstile` | CloudFlare Turnstile                                       | `useCloudflareTurnstile` | [Nuxt Cloudflare Turnstile](https://github.com/nuxt-modules/turnstile) |

More coming soon!

[//]: # (TODO | `twitter-pixel` | Twitter Pixel | `useTwitterPixel` |)
[//]: # (TODO | `pinterest-tag` | Pinterest Tag | `usePinterestTag` |)
[//]: # (TODO | `google-ads-conversion-tracking` | Google Ads Conversion Tracking | `useGoogleAdsConversionTracking` |)
[//]: # (TODO | `google-ads-remarketing` | Google Ads Remarketing | `useGoogleAdsRemarketing` |)
[//]: # (TODO | `plausible-analytics` | Plausible Analytics | `usePlausibleAnalytics` |)
[//]: # (TODO | `simple-analytics` | Simple Analytics | `useSimpleAnalytics` |)
[//]: # (TODO | `umami-analytics` | Umami Analytics | `useUmamiAnalytics` |)
[//]: # (TODO | `cloudflare-web-analytics` | Cloudflare Web Analytics | `useCloudflareWebAnalytics` |)
[//]: # (TODO | `matomo` | Matomo | `useMatomo` |)

### Loading Scripts Globally

If you prefer a config based approach, you can load scripts globally by defining them in your `nuxt.config.ts`.

```ts
export default defineNuxtConfig({
  scripts: {
    globals: [
      'https://cdn.jsdelivr.net/npm/js-confetti@latest/dist/js-confetti.browser.js',
      {
        assetStrategy: 'bundle'
      }
    ]
  }
})
```

### Bundling Scripts

Bundling scripts can allow you to serve them from your own server, improving privacy and performance. It
can also help to get around ad blockers and other privacy tools when you need a script to load.

You can opt-in to have your scripts bundled by using the `assetStrategy` option. As this is
analyzed at build time, you must define it statically.

```ts
useScript('https://cdn.jsdelivr.net/npm/js-confetti@latest/dist/js-confetti.browser.js', {
  assetStrategy: 'bundle'
})
// js-confetti.browser.js will be downloaded and bundled with your app as a static asset
```

### Overriding Scripts

When working with modules that use Nuxt Script, you may want to modify the
behavior of the script. This is especially useful for
changing the asset strategy of a script as it needs to be defined statically.

To do so you can use the `overrides` module option.

```ts
export default defineNuxtConfig({
  scripts: {
    overrides: {
      // the key is either a specified key or the script src
      confetti: {
        assetStrategy: 'bundle'
      }
    }
  }
})
```

### Privacy and Cookie Consent

Nuxt Scripts provides a `createScriptConsentTrigger` composable that allows you to load scripts based on user's consent.

You can either use it by providing a resolvable consent (ref, promise) option or by using `accept()`.

```ts
export const agreedToCookiesScriptConsent = createScriptConsentTrigger()
// ...
useScript('https://www.google-analytics.com/analytics.js', {
  trigger: agreedToCookiesScriptConsent
})
// ...
agreedToCookiesScriptConsent.accept()
```

```ts
const agreedToCookies = ref(false)
useScript('https://www.google-analytics.com/analytics.js', {
  // will be loaded in when the ref is true
  trigger: createScriptConsentTrigger({
    consent: agreedToCookies
  })
})
```

### Sending Page Events

When using tracking scripts, it's common to send an event when the page changes. Due to Nuxt's head implementation being
async, the page title is not always available on route change immediately.

`useAnalyticsPageEvent` solves this by providing you with the page title and path when they change.

```ts
useAnalyticsPageEvent(({ title, path }) => {
  // triggered on route change
  gtag('event', 'page_view', {
    page_title: title,
    page_location: 'https://example.com',
    page_path: path
  })
})
```

## API

### `useScript`

Please see the [useScript](https://unhead.unjs.io/usage/composables/use-script) documentation.

### `createScriptConsentTrigger`

**(options: ScriptConsentTriggerOptions) => { accept: () => void } & Promise<void>**

Creates a consent trigger for a script.

#### Arguments

- `consent` (optional) - A ref, promise, or boolean that resolves to the user's consent. Defaults to `undefined`.
- `loadOnNuxtReady` (optional) - If consent is provided before the browser idle, wait for the browser to be idle before loading the script. Defaults to `false`.

#### Returns

- `accept` - A function that can be called to accept the consent and load the script.

```ts
const trigger = createScriptConsentTrigger()
// accept the consent and load the script
trigger.accept()
```

### `useAnalyticsPageEvent`

**(callback?: (page: { title: string, path: string }) => void) => Ref<{ title: string, path: string }>**

Access the current page title and path and trigger an event when they change.

#### Arguments

- `callback` (optional) - A function that will be called when the page title or path changes.

#### Returns

- A ref containing the current page title and path.

```ts
const pageCtx = useAnalyticsPageEvent()
// will always be the current page title
pageCtx.value.title
```

## License

Licensed under the [MIT license](https://github.com/nuxt/scripts/blob/main/LICENSE.md).

## 📑 License

Published under the [MIT License](./LICENSE)

<!-- Badges -->
[npm-version-src]: https://img.shields.io/npm/v/@nuxt/scripts/latest.svg?style=flat&colorA=18181B&colorB=28CF8D
[npm-version-href]: https://npmjs.com/package/@nuxt/scripts/v/rc

[npm-downloads-src]: https://img.shields.io/npm/dm/@nuxt/scripts.svg?style=flat&colorA=18181B&colorB=28CF8D
[npm-downloads-href]: https://npmjs.com/package/@nuxt/scripts/v/rc

[license-src]: https://img.shields.io/npm/l/@nuxt/scripts.svg?style=flat&colorA=18181B&colorB=28CF8D
[license-href]: https://npmjs.com/package/@nuxt/scripts/v/rc

[nuxt-src]: https://img.shields.io/badge/Nuxt-18181B?logo=nuxt.js
[nuxt-href]: https://nuxt.com

[volta-src]: https://user-images.githubusercontent.com/904724/209143798-32345f6c-3cf8-4e06-9659-f4ace4a6acde.svg
[volta-href]: https://volta.net/nuxt/scripts?utm_source=nuxt_scripts_readme
