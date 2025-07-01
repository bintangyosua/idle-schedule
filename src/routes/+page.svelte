<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Delightful Schedule Wheel</title>
		<style>
			@import url('https://fonts.googleapis.com/css2?family=Caveat:wght@400;600;700&family=Inter:wght@400;500;600&display=swap');

			* {
				margin: 0;
				padding: 0;
				box-sizing: border-box;
			}

			body {
				font-family: 'Inter', sans-serif;
				min-height: 100vh;
				display: flex;
				align-items: center;
				justify-content: center;
				padding: 20px;
			}

			.schedule-container {
				backdrop-filter: blur(20px);
				border-radius: 30px;
				padding: 40px;
				max-width: 900px;
				width: 100%;
				position: relative;
				overflow: hidden;
			}

			.schedule-container::before {
				content: '';
				position: absolute;
				top: -50%;
				left: -50%;
				width: 200%;
				height: 200%;
				background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.1), transparent);
				animation: shimmer 8s infinite;
				pointer-events: none;
			}

			@keyframes shimmer {
				0% {
					transform: rotate(0deg);
				}
				100% {
					transform: rotate(360deg);
				}
			}

			.title {
				text-align: center;
				margin-bottom: 30px;
				font-family: 'Caveat', cursive;
				font-size: 2.5rem;
				font-weight: 700;
				color: #4a5568;
				position: relative;
				z-index: 1;
			}

			.responsive-svg {
				width: 100%;
				height: auto;
				max-width: 600px;
				margin: 0 auto;
				display: block;
				filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.1));
			}

			.segment-path {
				transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
				cursor: pointer;
				transform-origin: 250px 250px;
			}

			.segment-path:hover {
				transform: scale(1.05);
				filter: brightness(1.1) saturate(1.2);
			}

			.segment-label {
				font-size: 0.65em;
				fill: #2d3748;
				font-weight: 600;
				text-shadow: 0px 1px 3px rgba(255, 255, 255, 0.9);
				font-family: 'Inter', sans-serif;
				pointer-events: none;
				opacity: 0;
				animation: fadeInUp 0.6s ease-out forwards;
			}

			.segment-label tspan {
				font-size: 1em;
			}

			@keyframes fadeInUp {
				from {
					opacity: 0;
					transform: translateY(10px);
				}
				to {
					opacity: 1;
					transform: translateY(0);
				}
			}

			.play-button {
				cursor: pointer;
				transition: transform 0.25s ease;
				animation: subtlePulse 3s ease-in-out infinite;
				transform-origin: center;
			}

			.play-button:hover {
				transform: scale(1.1);
			}

			@keyframes subtlePulse {
				0%,
				100% {
					filter: drop-shadow(0 0 4px rgba(255, 255, 255, 0.1));
				}
				50% {
					filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.2));
				}
			}

			.play-label {
				font-size: 1.6em;
				font-weight: 600;
				fill: #edf2f4;
				font-family: 'Inter', 'Segoe UI', sans-serif;
				letter-spacing: 1px;
				pointer-events: none;
			}

			.hour-label {
				font-size: 0.75em;
				fill: #4a5568;
				font-weight: 600;
				font-family: 'Inter', sans-serif;
				opacity: 0;
				animation: fadeIn 1s ease-out forwards;
			}

			@keyframes fadeIn {
				from {
					opacity: 0;
				}
				to {
					opacity: 1;
				}
			}

			.hour-tick {
				stroke: #718096;
				stroke-width: 2;
				opacity: 0;
				animation: drawTick 0.5s ease-out forwards;
			}

			@keyframes drawTick {
				from {
					stroke-dasharray: 10;
					stroke-dashoffset: 10;
					opacity: 0;
				}
				to {
					stroke-dasharray: 10;
					stroke-dashoffset: 0;
					opacity: 1;
				}
			}

			.floating-elements {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				pointer-events: none;
				overflow: hidden;
			}

			.floating-star {
				position: absolute;
				color: #ffd700;
				font-size: 1.5rem;
				animation: float 6s ease-in-out infinite;
				opacity: 0.7;
			}

			@keyframes float {
				0%,
				100% {
					transform: translateY(0px) rotate(0deg);
					opacity: 0.7;
				}
				50% {
					transform: translateY(-20px) rotate(180deg);
					opacity: 1;
				}
			}

			.stats-panel {
				margin-top: 30px;
				display: grid;
				grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
				gap: 20px;
				position: relative;
				z-index: 1;
			}

			.stat-card {
				background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
				color: white;
				padding: 20px;
				border-radius: 15px;
				text-align: center;
				box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
				transition: transform 0.3s ease;
			}

			.stat-card:hover {
				transform: translateY(-5px);
			}

			.stat-value {
				font-size: 2rem;
				font-weight: 700;
				font-family: 'Caveat', cursive;
			}

			.stat-label {
				font-size: 0.9rem;
				opacity: 0.9;
				margin-top: 5px;
			}

			@media (max-width: 768px) {
				.schedule-container {
					padding: 20px;
				}

				.title {
					font-size: 2rem;
				}

				.segment-label {
					font-size: 0.5em;
				}

				.hour-label {
					font-size: 0.6em;
				}
			}
		</style>
	</head>
	<body>
		<div class="schedule-container">
			<div class="floating-elements">
				<div class="floating-star" style="top: 10%; left: 10%; animation-delay: 0s;">✨</div>
				<div class="floating-star" style="top: 20%; right: 15%; animation-delay: 2s;">⭐</div>
				<div class="floating-star" style="bottom: 15%; left: 20%; animation-delay: 4s;">🌟</div>
				<div class="floating-star" style="bottom: 25%; right: 10%; animation-delay: 1s;">✨</div>
			</div>

			<h1 class="title">Jadwal Menganggur Minuettaro</h1>

			<svg viewBox="0 0 500 500" class="responsive-svg">
				<defs>
					<filter id="glow">
						<feGaussianBlur stdDeviation="3" result="coloredBlur" />
						<feMerge>
							<feMergeNode in="coloredBlur" />
							<feMergeNode in="SourceGraphic" />
						</feMerge>
					</filter>
					<radialGradient id="centerGradient" cx="50%" cy="50%" r="50%">
						<stop offset="0%" style="stop-color:#ffd700;stop-opacity:1" />
						<stop offset="100%" style="stop-color:#ffa000;stop-opacity:1" />
					</radialGradient>
				</defs>

				<!-- Segments -->
				<g class="segments">
					<!-- Mimpi Indah -->
					<path
						class="segment-path"
						d="M 250,250 L 250,50 A 200,200 0 0 1 353.55,103.93 Z"
						fill="#B0E0E6"
						stroke="#fff"
						stroke-width="2"
						data-segment="0"
					/>

					<!-- Mengumpulkan Energi -->
					<path
						class="segment-path"
						d="M 250,250 L 353.55,103.93 A 200,200 0 0 1 396.96,146.45 Z"
						fill="#FFFACD"
						stroke="#fff"
						stroke-width="2"
						data-segment="1"
					/>

					<!-- Mandi, Makan, Minum, Scroll Dumping -->
					<path
						class="segment-path"
						d="M 250,250 L 396.96,146.45 A 200,200 0 0 1 441.42,306.07 Z"
						fill="#FFDAB9"
						stroke="#fff"
						stroke-width="2"
						data-segment="2"
					/>

					<!-- Baca Buku -->
					<path
						class="segment-path"
						d="M 250,250 L 441.42,306.07 A 200,200 0 0 1 396.96,353.55 Z"
						fill="#C8E6C9"
						stroke="#fff"
						stroke-width="2"
						data-segment="3"
					/>

					<!-- Freelancing -->
					<path
						class="segment-path"
						d="M 250,250 L 396.96,353.55 A 200,200 0 0 1 103.93,396.96 Z"
						fill="#B3E0FF"
						stroke="#fff"
						stroke-width="2"
						data-segment="4"
					/>

					<!-- Melamun -->
					<path
						class="segment-path"
						d="M 250,250 L 103.93,396.96 A 200,200 0 0 1 58.58,306.07 Z"
						fill="#ADD8E6"
						stroke="#fff"
						stroke-width="2"
						data-segment="5"
					/>

					<!-- Menikmati Senja -->
					<path
						class="segment-path"
						d="M 250,250 L 58.58,306.07 A 200,200 0 0 1 41.32,275 Z"
						fill="#FFB6C1"
						stroke="#fff"
						stroke-width="2"
						data-segment="6"
					/>

					<!-- Meratapi Hidup -->
					<path
						class="segment-path"
						d="M 250,250 L 41.32,275 A 200,200 0 0 1 41.32,225 Z"
						fill="#D3D3D3"
						stroke="#fff"
						stroke-width="2"
						data-segment="7"
					/>

					<!-- Melamun -->
					<path
						class="segment-path"
						d="M 250,250 L 41.32,225 A 200,200 0 0 1 86.61,116.12 Z"
						fill="#E6E6FA"
						stroke="#fff"
						stroke-width="2"
						data-segment="8"
					/>

					<!-- Scroll Dumping -->
					<path
						class="segment-path"
						d="M 250,250 L 86.61,116.12 A 200,200 0 0 1 193.93,58.58 Z"
						fill="#B0C4DE"
						stroke="#fff"
						stroke-width="2"
						data-segment="9"
					/>

					<!-- Sleep continuation -->
					<path
						class="segment-path"
						d="M 250,250 L 193.93,58.58 A 200,200 0 0 1 250,50 Z"
						fill="#B0E0E6"
						stroke="#fff"
						stroke-width="2"
						data-segment="10"
					/>
				</g>

				<!-- Segment Labels -->
				<text
					x="250"
					y="100"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.1s;"
				>
					<tspan x="250" dy="0em">Mimpi Indah</tspan>
				</text>

				<text
					x="375"
					y="125"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.2s;"
				>
					<tspan x="375" dy="0em">Mengumpulkan</tspan>
					<tspan x="375" dy="1.2em">Energi</tspan>
				</text>

				<text
					x="415"
					y="225"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.3s;"
				>
					<tspan x="415" dy="-0.6em">Mandi, Makan,</tspan>
					<tspan x="415" dy="1.2em">Minum, Scroll</tspan>
					<tspan x="415" dy="1.2em">Dumping</tspan>
				</text>

				<text
					x="420"
					y="330"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.4s;"
				>
					<tspan x="420" dy="0em">Baca Buku</tspan>
				</text>

				<text
					x="250"
					y="390"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.5s;"
				>
					<tspan x="250" dy="0em">Freelancing</tspan>
				</text>

				<text
					x="80"
					y="350"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.6s;"
				>
					<tspan x="80" dy="0em">Melamun</tspan>
				</text>

				<text
					x="50"
					y="290"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.7s;"
				>
					<tspan x="50" dy="-0.6em">Menikmati</tspan>
					<tspan x="50" dy="1.2em">Senja</tspan>
				</text>

				<text
					x="50"
					y="250"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.8s;"
				>
					<tspan x="50" dy="-0.6em">Meratapi</tspan>
					<tspan x="50" dy="1.2em">Hidup</tspan>
				</text>

				<text
					x="80"
					y="170"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 0.9s;"
				>
					<tspan x="80" dy="0em">Melamun</tspan>
				</text>

				<text
					x="140"
					y="90"
					text-anchor="middle"
					dominant-baseline="middle"
					class="segment-label"
					style="animation-delay: 1.0s;"
				>
					<tspan x="140" dy="-0.6em">Scroll</tspan>
					<tspan x="140" dy="1.2em">Dumping</tspan>
				</text>

				<!-- Center Button -->
				<g class="play-button">
					<circle cx="250" cy="250" r="42" fill="#2b2d42" stroke="#8d99ae" stroke-width="3" />
					<text x="250" y="250" text-anchor="middle" dominant-baseline="middle" class="play-label"
						>PLAY</text
					>
				</g>

				<!-- Hour Labels -->
				<g class="hour-labels">
					<text
						x="250"
						y="25"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.1s;">0</text
					>
					<text
						x="320"
						y="40"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.2s;">2</text
					>
					<text
						x="380"
						y="80"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.3s;">4</text
					>
					<text
						x="420"
						y="140"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.4s;">6</text
					>
					<text
						x="435"
						y="210"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.5s;">8</text
					>
					<text
						x="420"
						y="280"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.6s;">10</text
					>
					<text
						x="375"
						y="340"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.7s;">12</text
					>
					<text
						x="310"
						y="390"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.8s;">14</text
					>
					<text
						x="240"
						y="425"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 0.9s;">16</text
					>
					<text
						x="170"
						y="430"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 1.0s;">18</text
					>
					<text
						x="110"
						y="400"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 1.1s;">20</text
					>
					<text
						x="70"
						y="350"
						text-anchor="middle"
						dominant-baseline="middle"
						class="hour-label"
						style="animation-delay: 1.2s;">22</text
					>
				</g>

				<!-- Hour Ticks -->
				<g class="hour-ticks">
					<line
						x1="250"
						y1="45"
						x2="250"
						y2="55"
						class="hour-tick"
						style="animation-delay: 0.1s;"
					/>
					<line
						x1="353"
						y1="59"
						x2="348"
						y2="68"
						class="hour-tick"
						style="animation-delay: 0.2s;"
					/>
					<line
						x1="432"
						y1="147"
						x2="423"
						y2="152"
						class="hour-tick"
						style="animation-delay: 0.3s;"
					/>
					<line
						x1="455"
						y1="250"
						x2="445"
						y2="250"
						class="hour-tick"
						style="animation-delay: 0.4s;"
					/>
					<line
						x1="432"
						y1="353"
						x2="423"
						y2="348"
						class="hour-tick"
						style="animation-delay: 0.5s;"
					/>
					<line
						x1="353"
						y1="441"
						x2="348"
						y2="432"
						class="hour-tick"
						style="animation-delay: 0.6s;"
					/>
					<line
						x1="250"
						y1="455"
						x2="250"
						y2="445"
						class="hour-tick"
						style="animation-delay: 0.7s;"
					/>
					<line
						x1="147"
						y1="441"
						x2="152"
						y2="432"
						class="hour-tick"
						style="animation-delay: 0.8s;"
					/>
					<line
						x1="68"
						y1="353"
						x2="77"
						y2="348"
						class="hour-tick"
						style="animation-delay: 0.9s;"
					/>
					<line
						x1="45"
						y1="250"
						x2="55"
						y2="250"
						class="hour-tick"
						style="animation-delay: 1.0s;"
					/>
					<line
						x1="68"
						y1="147"
						x2="77"
						y2="152"
						class="hour-tick"
						style="animation-delay: 1.1s;"
					/>
					<line
						x1="147"
						y1="59"
						x2="152"
						y2="68"
						class="hour-tick"
						style="animation-delay: 1.2s;"
					/>
				</g>
			</svg>

			<!-- <div class="stats-panel">
				<div class="stat-card">
					<div class="stat-value">24</div>
					<div class="stat-label">Jam Total</div>
				</div>
				<div class="stat-card">
					<div class="stat-value">6</div>
					<div class="stat-label">Jam Tidur</div>
				</div>
				<div class="stat-card">
					<div class="stat-value">5</div>
					<div class="stat-label">Jam Kerja</div>
				</div>
				<div class="stat-card">
					<div class="stat-value">13</div>
					<div class="stat-label">Jam Santai</div>
				</div>
			</div> -->
		</div>

		<script>
			// Add interactive hover effects
			document.querySelectorAll('.segment-path').forEach((segment, index) => {
				segment.addEventListener('mouseenter', function () {
					this.style.transform = 'scale(1.05)';
					this.style.filter =
						'brightness(1.2) saturate(1.3) drop-shadow(0 5px 15px rgba(0,0,0,0.3))';
				});

				segment.addEventListener('mouseleave', function () {
					this.style.transform = 'scale(1)';
					this.style.filter = 'none';
				});

				// Add click effect
				segment.addEventListener('click', function () {
					this.style.animation = 'bounce 0.6s ease';
					setTimeout(() => {
						this.style.animation = '';
					}, 600);
				});
			});

			// Add bounce animation
			const style = document.createElement('style');
			style.textContent = `
            @keyframes bounce {
                0%, 20%, 60%, 100% { transform: scale(1); }
                40% { transform: scale(1.1); }
                80% { transform: scale(1.05); }
            }
        `;
			document.head.appendChild(style);

			// Play button interaction
			document.querySelector('.play-button').addEventListener('click', function () {
				const button = this;
				button.style.animation = 'spin 1s ease-in-out';

				// Add spin animation
				const spinStyle = document.createElement('style');
				spinStyle.textContent = `
                @keyframes spin {
                    from { transform: rotate(0deg) scale(1); }
                    50% { transform: rotate(180deg) scale(1.2); }
                    to { transform: rotate(360deg) scale(1); }
                }
            `;
				document.head.appendChild(spinStyle);

				setTimeout(() => {
					button.style.animation = 'pulse 2s infinite';
				}, 1000);
			});

			// Add floating animation to stars
			function createFloatingStars() {
				setInterval(() => {
					const colors = ['✨', '⭐', '🌟', '💫'];
					const star = document.createElement('div');
					star.className = 'floating-star';
					star.textContent = colors[Math.floor(Math.random() * colors.length)];
					star.style.left = Math.random() * 100 + '%';
					star.style.top = Math.random() * 100 + '%';
					star.style.animationDuration = Math.random() * 4 + 3 + 's';

					document.querySelector('.floating-elements').appendChild(star);

					setTimeout(() => {
						star.remove();
					}, 6000);
				}, 3000);
			}

			// Start floating stars
			createFloatingStars();

			// Add smooth scroll reveal for stats
			const observer = new IntersectionObserver((entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						entry.target.style.opacity = '1';
						entry.target.style.transform = 'translateY(0)';
					}
				});
			});

			document.querySelectorAll('.stat-card').forEach((card, index) => {
				card.style.opacity = '0';
				card.style.transform = 'translateY(20px)';
				card.style.transition = `opacity 0.6s ease ${index * 0.1}s, transform 0.6s ease ${index * 0.1}s`;
				observer.observe(card);
			});
		</script>
	</body>
</html>
