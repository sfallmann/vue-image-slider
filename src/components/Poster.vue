<template>
	<div class="poster-wrapper" :style="{ width: width || '33vw' }">
		<div class="poster-box" :class="{ flipped: clicked }" @click="$emit('clickedPoster');">
			<b-img-lazy class="poster-image" :src="src" />
			<div class="poster-back">
				<div class="poster-overlay">
					<span class="poster-details"> {{ details.split('.').join('.\n\n') }} </span>
				</div>
				<img class="poster-image" :src="src" />
			</div>
		</div>
	</div>
</template>
<script>
export default {
	props: ['width', 'details', 'src', 'clicked']
};
</script>
<style lang="scss">
.poster-wrapper {
	cursor: pointer;
	max-width: 400px;
	min-width: 150px;
	perspective: 800px;
	position: absolute;
	top: 20px;
	left: 50%;
	transform: translateX(-50%);
	z-index: 15;
}
.poster-box {
	position: absolute;
	transition-timing-function: ease-in;
	transition: transform 1.5s;
	transform-style: preserve-3d;
	transform-origin: center right;
	border: 4px solid white;
	border-radius: 3px;
	&:hover {
		border: 4px solid #61c76d;
	}
	box-sizing: content-box;
}

.poster-image {
	width: 100%;
	height: 100%;
}
.poster-back {
	position: absolute;
	z-index: 20;
	background-color: lighten(black, 15%);
	color: white;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
	backface-visibility: hidden;
	transform: rotateY(180deg);
}
.poster-overlay {
	position: absolute;
	z-index: 25;
	background-color: darken(#61c76d, 45%);
	opacity: 0.95;
	color: white;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
	display: flex;
	padding: 15px;
}
.poster-details {
	opacity: 1;
	font-size: medium;
	line-height: 1.75;
	font-weight: 700;
	text-align: center;
	align-self: center;
	white-space: pre-line;
}

.flipped {
	transform: rotateY(-180deg);
	transform: translateX(-100%) rotateY(-180deg);
}

@media (max-height: 835px) {
	.poster-wrapper {
		top: 50px;
		width: 200px;
		perspective: 800px;
		left: 50%;
		transform: translateX(-50%);
	}
}
</style>
