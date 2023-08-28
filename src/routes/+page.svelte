<!-- 타입스크립트 사용하려면 lang="ts" 추가해야함. 참고 - https://svelte.dev/blog/svelte-and-typescript -->
<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { Map, Marker, Popup } from 'mapbox-gl';
	import '../../node_modules/mapbox-gl/dist/mapbox-gl.css';
	import MapboxLanguage from '@mapbox/mapbox-gl-language';

	const SOOMINS_ACCESS_TOKEN =
		'pk.eyJ1IjoidG9tLXRpbW15IiwiYSI6ImNsbHVyNmtqMjBnenIzcG1udWRxaGNsc3MifQ.bYy4m8fOtyG2j7pKePlxmw';

	let map: Map;
	let mapContainer: string | HTMLElement;
	let lng: number, lat: number, zoom: number;

	const FUKUSHIMA_COORDINATES = {
		lng: 141.0328,
		lat: 37.4211
	};

	lng = FUKUSHIMA_COORDINATES.lng;
	lat = FUKUSHIMA_COORDINATES.lat;
	zoom = 4.5;

	function updateData() {
		zoom = map.getZoom();
		lng = map.getCenter().lng;
		lat = map.getCenter().lat;
	}

	onMount(() => {
		const initialState = { lng: lng, lat: lat, zoom: zoom };

		// 지도 생성
		map = new Map({
			container: mapContainer,
			accessToken: SOOMINS_ACCESS_TOKEN,
			style: `mapbox://styles/mapbox/outdoors-v11`,
			center: [initialState.lng, initialState.lat],
			zoom: initialState.zoom
		});

		const language = new MapboxLanguage({ supportedLanguages: ['ko'], defaultLanguage: 'ko' });
		map.addControl(language);

		// 마커 추가
		new Marker({ color: 'red' })
			.setLngLat([FUKUSHIMA_COORDINATES.lng, FUKUSHIMA_COORDINATES.lat])
			.setPopup(new Popup().setHTML('<h2>후쿠시마 원전</h2>'))
			.addTo(map);

		map.on('move', () => {
			updateData();
		});
	});

	onDestroy(() => {
		// map.remove(); //➡️ remove 메서드가 없다는디?
		map?.remove();
	});
</script>

<div>
	<h1 class="title">후쿠시마 오염수 실시간 지도</h1>
	<p class="version">버전 23.08.28.b</p>
	<table class="desc">
		<tbody>
			<tr>
				<td>
					<a href="https://github.com/smnchoi">@smnchoi</a> |
					<a href="https://github.com/minSsan">@minSsan</a> |
					<a href="https://github.com/hojkim77">@hojkim77</a> |
					<a href="https://github.com/matmang">@matmang</a>
				</td>
				<td><a href="https://caregiver.pet">Team CareGiver</a></td>
			</tr>
		</tbody>
	</table>

	<!-- 지도 -->
	<div class="map-wrap">
		<div class="map" bind:this={mapContainer} />
	</div>

	<!-- 좌표 사이드바 -->
	<div class="sidebar">
		Longitude: {lng.toFixed(4)} | Latitude: {lat.toFixed(4)} | Zoom: {zoom.toFixed(2)}
	</div>
</div>

<style>
	@font-face {
		font-family: 'GangwonState';
		src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_2307-2@1.0/GangwonState.woff2')
			format('woff2');
		font-weight: normal;
		font-style: normal;
	}

	h1.title {
		text-align: center;
		font-size: 4rem;
		font-weight: bold;
		margin: 1rem 0;
		font-family: 'GangwonState';

		text-decoration: underline;
		text-decoration-color: #e2c249;
		text-underline-offset: 16px;
	}

	p.version {
		text-align: center;
		font-size: 0.8rem;
		font-weight: bold;
		margin: 1rem 0;
		font-family: 'GangwonState';
	}

	table.desc {
		width: 100%;
		font-size: 0.8rem;
		font-family: 'GangwonState';
		align-items: center;
		margin: 1rem 0;
	}

	table.desc td:first-child {
		text-align: left;
	}

	table.desc td:nth-child(2) {
		text-align: right;
	}

	a {
		color: #e2c249;
		text-decoration: none;

		&:hover {
			text-decoration: underline;
		}
	}

	.map {
		position: absolute;
		width: 100%;
		height: 80%;
	}

	.sidebar {
		background-color: rgba(35, 55, 75, 0.9);
		color: #fff;
		padding: 6px 12px;
		font-family: monospace;
		z-index: 1;
		position: absolute;
		bottom: 20px;
		left: 0;
		margin: 12px;
		border-radius: 4px;
	}
</style>
