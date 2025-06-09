<script lang="ts">
	import { onMount } from 'svelte';

	// export let name = 'Minuettaro'; // Nama bisa diubah dari luar
	// export let title = 'Jadwal Menganggur'; // Judul bisa diubah dari luar

	// Data untuk setiap segmen jam
	const segments = [
		{ startHour: 0, endHour: 6, label: 'Mimpi Indah', color: '#B0E0E6' }, // Powder Blue (slightly more vibrant than light blue)
		{ startHour: 6, endHour: 7, label: 'Mengumpulkan Energi', color: '#FFFACD' }, // Lemon Chiffon (soft, warm yellow)
		{ startHour: 7, endHour: 9.5, label: 'Mandi, Makan, Minum,\nScroll Dumping', color: '#FFDAB9' }, // Peach Puff (warm, inviting orange)
		{ startHour: 9.5, endHour: 11, label: 'Baca Buku', color: '#C8E6C9' }, // Light Green (calming and fresh)
		{ startHour: 11, endHour: 16, label: 'Freelancing', color: '#B3E0FF' }, // Light Sky Blue (professional yet light)
		{ startHour: 16, endHour: 17, label: 'Melamun', color: '#ADD8E6' }, // Light Blue (airy and contemplative)
		{ startHour: 17, endHour: 18, label: 'Menikmati Senja', color: '#FFB6C1' }, // Light Pink (warm and gentle for sunset)
		{ startHour: 18, endHour: 19, label: 'Meratapi Hidup', color: '#D3D3D3' }, // Light Gray (more neutral and reflective)
		{ startHour: 19, endHour: 20.5, label: 'Melamun', color: '#E6E6FA' }, // Lavender (soft and dreamy)
		{ startHour: 20.5, endHour: 22.5, label: 'Scroll Dumping', color: '#B0C4DE' }, // Light Steel Blue (slightly muted and cool)
		{ startHour: 22.5, endHour: 24, label: '', color: '#B0E0E6' } // Powder Blue (returning to sleep color)
	];

	const radius = 200; // Radius jam
	const centerX = 250;
	const centerY = 250;

	// Fungsi untuk mendapatkan koordinat di lingkaran
	function getCoordinatesForHour(hour: number, r: number) {
		const angle = (hour / 24) * 360 - 90; // -90 agar jam 0 di atas
		const radians = (angle * Math.PI) / 180;
		return {
			x: centerX + r * Math.cos(radians),
			y: centerY + r * Math.sin(radians)
		};
	}

	// Fungsi untuk mendapatkan path segmen lingkaran (pie slice)
	function getPathForSegment(startHour: number, endHour: number, r: number) {
		const startCoords = getCoordinatesForHour(startHour, r);
		const endCoords = getCoordinatesForHour(endHour, r);

		const largeArcFlag = endHour - startHour > 12 ? 1 : 0; // Lebih dari 12 jam, gunakan busur besar

		return `M ${centerX},${centerY} L ${startCoords.x},${startCoords.y} A ${r},${r} 0 ${largeArcFlag} 1 ${endCoords.x},${endCoords.y} Z`;
	}

	// Fungsi untuk mendapatkan posisi teks di tengah segmen
	function getTextCoordinatesForSegment(startHour: number, endHour: number, r: number) {
		const midHour = (startHour + endHour) / 2;
		// Handle wrap-around for 23-0 segment
		const adjustedMidHour = startHour === 23 && endHour === 24 ? 23.5 : midHour;
		return getCoordinatesForHour(adjustedMidHour, r * 0.7); // Teks di 70% radius
	}

	// Posisi teks jam di luar lingkaran
	function getHourLabelCoordinates(hour: number, r: number) {
		return getCoordinatesForHour(hour, r + 20); // Sedikit di luar lingkaran
	}
</script>

<div class="schedule-container">
	<svg viewBox="0 0 500 500" class="responsive-svg">
		{#each segments as segment}
			<path
				d={getPathForSegment(segment.startHour, segment.endHour, radius)}
				fill={segment.color}
				stroke="#ccc"
				stroke-width="0"
			/>
			{@const textCoords = getTextCoordinatesForSegment(segment.startHour, segment.endHour, radius)}
			<text
				x={textCoords.x}
				y={textCoords.y}
				text-anchor="middle"
				dominant-baseline="middle"
				class="segment-label"
			>
				{#each segment.label.split('\n') as line, i}
					<tspan x={textCoords.x} dy={i === 0 ? '0em' : '1.2em'}>{line}</tspan>
				{/each}
			</text>
		{/each}

		<circle cx={centerX} cy={centerY} r="35" fill="#fdd835" stroke="#fbc02d" stroke-width="2" />
		<text x={centerX} y={centerY} text-anchor="middle" dominant-baseline="middle" class="play-label"
			>PLAY</text
		>

		{#each Array(24).fill(0) as _, i}
			{@const coords = getHourLabelCoordinates(i, radius)}
			<text
				x={coords.x}
				y={coords.y}
				text-anchor="middle"
				dominant-baseline="middle"
				class="hour-label"
			>
				{i}
			</text>
		{/each}

		{#each Array(24).fill(0) as _, i}
			{@const innerCoords = getCoordinatesForHour(i, radius - 5)}
			{@const outerCoords = getCoordinatesForHour(i, radius + 5)}
			<line
				x1={innerCoords.x}
				y1={innerCoords.y}
				x2={outerCoords.x}
				y2={outerCoords.y}
				stroke="#888"
				stroke-width="1"
			/>
		{/each}

		<g
			transform="translate({getCoordinatesForHour(0.5, radius * 0.8).x}, {getCoordinatesForHour(
				0.5,
				radius * 0.8
			).y})"
		>
			<path
				d="M -20,0 C -20,-10 -15,-20 0,-20 C 15,-20 20,-10 20,0 C 20,10 15,20 0,20 C -15,20 -20,10 -20,0 Z"
				fill="#fff9c4"
			/>
			<circle cx="5" cy="-5" r="5" fill="#fdd835" />
			<path
				d="M 10,0 C 10,-5 5,-10 0,-10 C -5,-10 -10,-5 -10,0 C -10,5 -5,10 0,10 C 5,10 10,5 10,0 Z"
				fill="#fdd835"
			/>

			<path
				d="M -30,10 C -40,10 -40,0 -30,0 C -20,0 -20,-10 -10,-10 C 0,-10 0,0 10,0 C 20,0 20,-10 30,-10 C 40,-10 40,0 30,0 C 20,0 20,10 10,10 C 0,10 0,20 -10,20 C -20,20 -20,10 -30,10 Z"
				fill="#eceff1"
				stroke="#cfd8dc"
				stroke-width="1"
			/>
		</g>
	</svg>
</div>

<style>
	.schedule-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 20px;
		font-family: sans-serif;
		/* Add max-width for the container to control overall size */
		max-width: 900px; /* Example: adjust as needed */
		margin: 0 auto; /* Center the container */
	}

	.title {
		margin-bottom: 20px;
		color: #333;
	}

	.responsive-svg {
		/* Set width to 100% of its parent container */
		width: 100%;
		/* Maintain aspect ratio. Height will adjust automatically based on viewBox */
		height: auto;
		/* Optional: Set a max-width if you don't want it to grow too large on big screens */
		max-width: 2500px; /* This corresponds to your viewBox width */
	}

	.segment-label {
		font-size: 0.6em;
		fill: #333;
		font-weight: bold;
		text-shadow: 0px 0px 2px rgba(255, 255, 255, 0.7); /* Untuk keterbacaan */
	}

	.play-label {
		font-size: 1.5em;
		font-weight: 900;
		fill: #333;
		font-family: 'Caveat Variable';
	}

	.hour-label {
		font-size: 0.7em;
		fill: #555;
		font-weight: bold;
	}
</style>
