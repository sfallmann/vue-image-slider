<template>
	<div id="app">
		<Poster
			:src="poster.src"
			:details="poster.details"
			:clicked="clickedSelected"
			@clickedPoster="clickedSelected = !clickedSelected;"
		/>
		<ImageNavBar
			v-if="movies.length"
			:list="movies"
			@updateSelected="handleUpdate"
			@clickedSelected="clickedSelected = !clickedSelected;"
		/>
	</div>
</template>

<script>
import Poster from './components/Poster';
import ImageNavBar from './components/ImageNavBar';
import json from './moviedata.json';

const imgUrl = 'https://image.tmdb.org/t/p/w500';

export default {
	mounted() {
		json.results.forEach(i => {
			i.src = `${imgUrl}${i.poster_path}`;
		});
		this.movies = json.results;
	},
	name: 'App',
	components: {
		Poster,
		ImageNavBar
	},
	data() {
		return {
			movies: [],
			poster: {
				src: '',
				details: ''
			},
			clickedSelected: false
		};
	},
	methods: {
		handleUpdate(selected) {
			console.log(selected);
			this.poster.src = selected.src;
			this.poster.details = selected.overview;
		}
	}
};
</script>
<style lang="scss">
body {
	height: 100vh;
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	background-color: lighten(black, 5%);
	font-family: 'Lato';
}
</style>
