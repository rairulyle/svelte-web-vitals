# Svelte wrapper for Web Vitals

An Svelte wrapper that provides a simple way to enable [Core Web Vitals](https://web.dev/vitals/) metrics for the following platforms:

- [Vercel - Speed Insights](https://vercel.com/docs/concepts/speed-insights/api)
- Others (coming soon)

<img src="https://raw.githubusercontent.com/rairulyle/svelte-web-vitals/main/static/vercel-preview.png">

## Note

As of the moment, the svelte wrapper is still in `development`. The current version only supports <strong>Vercel - Speed Insights</strong> as default. The next version will support other platforms such as Google Analytics, Google Tag Manager, and more.

## Prerequisites

An analytics integration is required to use this package. Currently, only [Vercel - Speed Insights](https://vercel.com/docs/concepts/speed-insights) is supported.

## Installation

```bash
npm i @rairulyle/svelte-web-vitals -D
```

## Usage

1. import WebVitals to your root `+layout.svelte` file.

```svelte
<script lang="ts">
	import WebVitals from '@rairulyle/svelte-web-vitals';
</script>

<WebVitals />
```

2. Define the Analytics ID via `vite.config.ts` file. As of the moment, only Vercel is supported.

```ts
export default defineConfig({
	// Other configs here...
	define: {
		'import.meta.env.VERCEL_ANALYTICS_ID': JSON.stringify(process.env.VERCEL_ANALYTICS_ID)
	}
});
```

## Roadmap

- [x] Vercel - Speed Insights
- [ ] Google Analytics
- [ ] Google Tag Manager
- [ ] Others
