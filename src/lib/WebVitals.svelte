<script lang="ts">
	import { browser } from '$app/environment';
	import { page } from '$app/stores';
	import { onCLS, onFCP, onFID, onLCP, onTTFB, type Metric } from 'web-vitals';

	const vitalsUrl = 'https://vitals.vercel-analytics.com/v1/vitals';

	interface MetricOption {
		// eslint-disable-next-line @typescript-eslint/no-explicit-any
		params: { [s: string]: any } | ArrayLike<any>;
		path: string;
		analyticsId: string;
		debug: boolean;
	}

	function getConnectionSpeed() {
		// @ts-ignore
		return 'connection' in navigator && navigator['connection'] && 'effectiveType' in navigator['connection']
			? navigator['connection']['effectiveType']
			: '';
	}

	function sendToAnalytics(metric: Metric, options: MetricOption) {
		const page = Object.entries(options.params).reduce((acc, [key, value]) => acc.replace(value, `[${key}]`), options.path);

		const body = {
			dsn: options.analyticsId,
			id: metric.id,
			page,
			href: location.href,
			event_name: metric.name,
			value: metric.value.toString(),
			speed: getConnectionSpeed()
		};

		if (options.debug) {
			console.log('[Web Vitals]', metric.name, JSON.stringify(body, null, 2));
		}

		// @ts-ignore
		const blob = new Blob([new URLSearchParams(body).toString()], {
			// This content type is necessary for `sendBeacon`
			type: 'application/x-www-form-urlencoded'
		});
		if (navigator.sendBeacon) {
			navigator.sendBeacon(vitalsUrl, blob);
		} else
			fetch(vitalsUrl, {
				body: blob,
				method: 'POST',
				credentials: 'omit',
				keepalive: true
			});
	}

	function webVitals(options: MetricOption) {
		try {
			onFID((metric) => sendToAnalytics(metric, options));
			onTTFB((metric) => sendToAnalytics(metric, options));
			onLCP((metric) => sendToAnalytics(metric, options));
			onCLS((metric) => sendToAnalytics(metric, options));
			onFCP((metric) => sendToAnalytics(metric, options));
		} catch (err) {
			console.error('[Web Vitals]', err);
		}
	}

	let analyticsId = import.meta.env.VERCEL_ANALYTICS_ID;

	$: if (browser && analyticsId) {
		webVitals({
			path: $page.url.pathname,
			params: $page.params,
			analyticsId,
			debug: false
		});
	}
</script>
