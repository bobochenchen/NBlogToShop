<template>
	<header ref="header">
		<div class="carousel-container">
			<transition-group name="carousel-fade" tag="div" class="carousel-slides">
				<div
					v-for="(img, index) in images"
					:key="index"
					v-show="index === currentIndex"
					class="carousel-slide"
					:style="{backgroundImage:'url('+img+')'}"
				></div>
			</transition-group>
			<!-- 指示器 -->
			<div class="carousel-dots" v-if="images.length > 1">
				<span
					v-for="(img, index) in images"
					:key="index"
					:class="['dot', { active: index === currentIndex }]"
					@click="goTo(index)"
				></span>
			</div>
		</div>
		<div class="text-malfunction" :data-word="defaultSettings.malfunctionText">
			{{ defaultSettings.malfunctionText }}
			<div class="line"></div>
		</div>
		<div class="wrapper">
			<i class="ali-iconfont icon-down" @click="scrollToMain"></i>
		</div>
		<div class="wave1"></div>
		<div class="wave2"></div>
	</header>
</template>

<script>
	import {mapState} from 'vuex'
	import defaultSettings from '@/settings'

	export default {
		name: "Header",
		data() {
			return {
				defaultSettings,
				images: [],
				currentIndex: 0,
				timer: null,
				interval: 5000
			}
		},
		computed: {
			...mapState(['clientSize'])
		},
		watch: {
			'clientSize.clientHeight'() {
				this.setHeaderHeight()
			}
		},
		created() {
			this.loadImages()
		},
		mounted() {
			this.setHeaderHeight()
			this.startAutoPlay()
		},
		beforeDestroy() {
			this.stopAutoPlay()
		},
		methods: {
			// 加载 homepage-imgs 目录下的所有图片
			loadImages() {
				const imgNames = [
					'16926.PNG',
					'16927.JPG',
					'16928.JPG',
					'16929.JPG',
					'16930.PNG',
					'16931.JPG',
					'16932.JPG',
					'16933.JPG',
					'16934.JPG'
				]
				this.images = imgNames.map(name => `/img/homepage-imgs/${name}`)
			},
			// 自动轮播
			startAutoPlay() {
				if (this.images.length <= 1) return
				this.stopAutoPlay()
				this.timer = setInterval(() => {
					this.next()
				}, this.interval)
			},
			stopAutoPlay() {
				if (this.timer) {
					clearInterval(this.timer)
					this.timer = null
				}
			},
			next() {
				this.currentIndex = (this.currentIndex + 1) % this.images.length
			},
			prev() {
				this.currentIndex = (this.currentIndex - 1 + this.images.length) % this.images.length
			},
			goTo(index) {
				this.currentIndex = index
				// 点击指示器后重置自动轮播计时
				this.startAutoPlay()
			},
			//根据可视窗口高度，动态改变首图大小
			setHeaderHeight() {
				this.$refs.header.style.height = this.clientSize.clientHeight + 'px'
			},
			//平滑滚动至正文部分
			scrollToMain() {
				window.scrollTo({top: this.clientSize.clientHeight, behavior: 'smooth'})
			}
		},
	}
</script>

<style scoped>
	header {
		user-select: none;
		position: relative;
		overflow: hidden;
	}

	.carousel-container {
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
	}

	.carousel-slides {
		position: relative;
		width: 100%;
		height: 100%;
	}

	.carousel-slide {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-position: center center;
		background-size: cover;
	}

	/* 淡入淡出过渡 */
	.carousel-fade-enter-active,
	.carousel-fade-leave-active {
		transition: opacity 1s ease;
	}

	.carousel-fade-enter,
	.carousel-fade-leave-to {
		opacity: 0;
	}

	/* 指示器 */
	.carousel-dots {
		position: absolute;
		bottom: 180px;
		left: 50%;
		transform: translateX(-50%);
		z-index: 90;
		display: flex;
		gap: 10px;
	}

	.dot {
		width: 12px;
		height: 12px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.5);
		cursor: pointer;
		transition: background 0.3s, transform 0.3s;
	}

	.dot:hover {
		background: rgba(255, 255, 255, 0.8);
	}

	.dot.active {
		background: #fff;
		transform: scale(1.2);
	}

	.text-malfunction {
		position: absolute;
		padding: 0 4px;
		top: 40%;
		left: 50.5%;
		transform: translate(-50%, -50%) scale(2.5);
		font-size: 34px;
		font-family: sans-serif;
		color: transparent;
	}

	.line {
		position: absolute;
		width: calc(100% - 8px);
		left: -0.5px;
		height: 1px;
		background: black;
		z-index: 50;
		animation: lineMove 5s ease-out infinite;
	}

	.text-malfunction:before, .text-malfunction:after {
		content: attr(data-word);
		position: absolute;
		top: 0;
		line-height: 50px;
		overflow: hidden;
		filter: contrast(200%);
	}

	.text-malfunction:before {
		left: 0;
		color: red;
		text-shadow: 1px 0 0 red;
		z-index: 30;
		animation: malfunctionAni 0.95s infinite;
	}

	.text-malfunction:after {
		left: -1px;
		color: cyan;
		text-shadow: -1px 0 0 cyan;
		z-index: 40;
		mix-blend-mode: lighten;
		animation: malfunctionAni 1.1s infinite 0.2s;
	}

	@keyframes lineMove {
		9% {
			top: 38px;
		}
		14% {
			top: 8px;
		}
		18% {
			top: 42px;
		}
		22% {
			top: 1px;
		}
		32% {
			top: 32px;
		}
		34% {
			top: 12px;
		}
		40% {
			top: 26px;
		}
		43% {
			top: 7px;
		}
		99% {
			top: 30px;
		}
	}

	@keyframes malfunctionAni {
		10% {
			top: -0.4px;
			left: -1.1px;
		}
		20% {
			top: 0.4px;
			left: -0.2px;
		}
		30% {
			left: .5px;
		}
		40% {
			top: -0.3px;
			left: -0.7px;
		}
		50% {
			left: 0.2px;
		}
		60% {
			top: 1.8px;
			left: -1.2px;
		}
		70% {
			top: -1px;
			left: 0.1px;
		}
		80% {
			top: -0.4px;
			left: -0.9px;
		}
		90% {
			left: 1.2px;
		}
		100% {
			left: -1.2px;
		}
	}

	.wrapper {
		position: absolute;
		width: 100px;
		bottom: 150px;
		left: 0;
		right: 0;
		margin: auto;
		font-size: 26px;
		z-index: 100;
	}

	.wrapper i {
		font-size: 60px;
		opacity: 0.5;
		cursor: pointer;
		position: absolute;
		top: 55px;
		left: 20px;
		animation: opener .5s ease-in-out alternate infinite;
		transition: opacity .2s ease-in-out, transform .5s ease-in-out .2s;
	}

	.wrapper i:hover {
		opacity: 1;
	}

	@keyframes opener {
		100% {
			top: 65px
		}
	}

	.wave1, .wave2 {
		position: absolute;
		bottom: 0;
		transition-duration: .4s, .4s;
		z-index: 80;
	}

	.wave1 {
		background: url('/img/header/wave1.png') repeat-x;
		height: 75px;
		width: 100%;
	}

	.wave2 {
		background: url('/img/header/wave2.png') repeat-x;
		height: 90px;
		width: calc(100% + 100px);
		left: -100px;
	}
</style>
