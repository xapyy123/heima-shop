<template>
	<view class="my-search-container">
		<view class="my-search-box" @click="searchHandler">
			<uni-icons type="search" size="17"></uni-icons>
			<text class="text">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-search",
		data() {
			return {

			};
		},
		methods:{
			searchHandler(){
				this.$emit('click')
			}
		}
	}
</script>

<style lang="scss">
	.my-search-container {
		display: flex;
		background-color: #c00000;
		height: 50px;
		text-align: center;
		align-items: center;
	}

	.my-search-box {
		width: 100%;
		height: 36px;
		display: flex;
		background-color: #ffffff;
		border-radius: 15px;
		align-items: center;
		justify-content: center;

		.text {
			text-align: center;
			font-size: 15px;
			margin-left: 5px;
		}
	}
</style>
