<template>
	<div class="image-navbar">
		<div class="list-wrapper">
			<div class="left-list">
				<transition-group name="flip-list" tag="div">
					<template v-for="(item, index) in left">
						<img
							class="image-thumbnail left"
							:src="item.src"
							:key="item.id"
							@click="imageClick('left', index);"
						/>
					</template>
				</transition-group>
			</div>
			<div class="button-box">
				<div class="direction-btn left" @click="move('left');">&#9664;</div>
			</div>
			<div class="selected-image-box">
				<img class="selected-image" @click="$emit('clickedSelected');" :src="selected.src" />
			</div>
			<div class="button-box">
				<div class="direction-btn right" @click="move('right');">&#9658;</div>
			</div>
			<div class="right-list">
				<transition-group name="flip-list" tag="div">
					<template v-for="(item, index) in right">
						<img
							class="image-thumbnail right"
							:src="item.src"
							:key="item.id"
							@click="imageClick('right', index);"
						/>
					</template>
				</transition-group>
			</div>
		</div>
	</div>
</template>
<script>
export default {
	props: ['list'],
	mounted() {
		this.initialSetup(this.list);
	},
	data() {
		return {
			selected: {},
			left: [],
			right: []
		};
	},
	methods: {
		move(dir) {
			if (dir === 'left') {
				this.left.push(this.selected);
				this.right.push(this.left.shift());
				this.selected = this.right.shift();
			} else {
				this.right.unshift(this.selected);
				this.left.unshift(this.right.pop());
				this.selected = this.left.pop();
			}
			this.$emit('updateSelected', this.selected);
		},
		initialSetup(list) {
			// get a copy of the prop 'list'
			const localList = list.concat();

			// sort it by popularity ascending
			// TODO: pass down a sort key as a prop
			localList.sort((a, b) => {
				return a.popularity - b.popularity;
			});

			// get the last item from the list - the most popular item
			//this.selected = localList.pop();
			const len = localList.length;

			// get the index of the middle of the list
			//const midIndex = len % 2 === 0 ? len / 2 : (len - 1) / 2;
			const midIndex = Math.round(len / 2);
			// split left and right off the middle index
			this.left = localList.splice(0, midIndex);
			this.right = localList.splice(0, len);
			this.selected = this.right.pop();
			this.right.unshift(this.left.pop());
			this.$emit('updateSelected', this.selected);
		},
		imageClick(side, index) {
			console.log(side, index);
			let selected;

			// save the reference to the clicked image
			// add this.selected to one of the sides
			// take an item from the side it was added
			// and either push or unshift it onto the other list
			if (side === 'left') {
				selected = this.left[index];
				this.right.unshift(this.selected);
				this.left.unshift(this.right.pop());
			} else {
				selected = this.right[index];
				this.left.push(this.selected);
				this.right.push(this.left.shift());
			}

			// set locaList to the combination of left and right
			const localList = this.left.concat(this.right);

			// get the index of the clicked image in the new list
			let itemIndex = localList.findIndex(i => i === selected);

			let len = localList.length;

			// get the middle index
			let midIndex = Math.round(len / 2);

			// get the difference between the middle index and the item of the clicked image
			const diff = itemIndex - midIndex;

			// if the diff is positive everything needs to move 'left'
			// if it's negative, everything needs to move right

			if (diff > 0) {
				for (let i = 0; i < Math.abs(diff); i++) {
					localList.push(localList.shift());
				}
			} else if (diff < 0) {
				for (let i = 0; i < Math.abs(diff); i++) {
					localList.unshift(localList.pop());
				}
			}

			// find the new index of the clicked image in the new list
			itemIndex = localList.findIndex(i => i === selected);

			// left gets everything up to the clicked image's index
			// right gets everything starting with the clicked image to the end
			// shift the clicked image off of right and assign this.selected to it
			this.left = localList.splice(0, itemIndex);
			this.right = localList.splice(0, localList.length);
			this.selected = this.right.shift();
			this.$emit('updateSelected', this.selected);
		}
	}
};
</script>
<style lang="scss">
.image-navbar {
	position: fixed;
	bottom: 0;
	width: 100%;
	border: 1px solid white;
	border-left: 0;
	border-right: 0;
	height: 200px;
}

.list-wrapper {
	display: flex;
	justify-content: center;
	position: absolute;
	left: 50%;
	transform: translateX(-50%);
	width: 100%;
}

.image-thumbnail {
	border: 2px solid white;
	width: 120px;
	margin: 10px;
	cursor: pointer;
}

.left-list {
	display: flex;
	justify-content: flex-end;
	width: 100%;
	height: 200px;
	overflow: hidden;
	white-space: nowrap;
	.image-thumbnail:last-of-type {
		margin-right: 0;
	}
}

.direction-btn {
	cursor: pointer;
	color: white;
	font-size: 80px;
	width: 250px;
	height: 100%;
	vertical-align: middle;
	line-height: 200px;
	user-select: none;

	&:hover {
		color: #add8e6;
	}

	&.right {
		text-align: right;
		margin-right: 20px;
	}

	&.left {
		text-align: left;
		margin-left: 20px;
	}
}

.selected-image-box {
	height: 100%;
	.selected-image {
		-webkit-box-shadow: 0px -1px 27px 6px rgba(97, 199, 109, 1);
		-moz-box-shadow: 0px -1px 27px 6px rgba(97, 199, 109, 1);
		box-shadow: 0px -1px 27px 6px rgba(97, 199, 109, 1);
		border: 3px solid #61c76d;
		border-radius: 3px;
		position: absolute;
		z-index: 10;
		bottom: 5px;
		left: 50%;
		transform: translateX(-50%);
		height: 210px;
		cursor: pointer;
	}
}

.right-list {
	display: flex;
	justify-content: flex-start;
	width: 100%;
	height: 200px;
	overflow: hidden;
	white-space: nowrap;
	.image-thumbnail:first-of-type {
		margin-left: 0;
	}
}

@media (max-height: 835px) {
	.direction-btn {
		margin-left: 50px;
		margin-right: 50px;
	}
	.image-navbar {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}

	.selected-image-box {
		display: none;
	}
}
</style>
