<template>
	<view>
		<view class="search-box">
			<my-search @click='gotoSearch'></my-search>
		</view>
		<!-- <!- 头部轮播图 -> -->
		<swiper :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,index) in swiperList" :key="index">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src"> </image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- <!- 头部轮播图 -> -->

		<!-- 分类导航 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,index) in navList" :key="index" @click="navHandlerChange(item)">
				<img :src="item.image_src" alt="" class="nav-img">
			</view>
		</view>
		<!-- 分类导航 -->

		<!-- 楼层 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item,index) in floorList" :key="index">
				<!-- 楼层标题 -->
				<image :src="item.floor_title.image_src" mode="" class="floor-title"></image>
				<!-- 楼层主体区域 -->
				<view class="floor-container-box">
					<!-- 左侧大图片 -->
					<view class="container-left">
						<navigator :url="item.product_list[0].url">
							<image :src="item.product_list[0].image_src" mode="widthFix"
								:style="{width: item.product_list[0].image_width + 'rpx'}"></image>
						</navigator>
					</view>
					<!-- 右侧小图片 -->
					<view class="container-right">
						<view class="container-right-item" v-for="(containerItem,index) in item.product_list"
							:key="index" v-if="index!=0">
							<navigator :url="containerItem.url">
								<image :src="containerItem.image_src" mode="widthFix"
									:style="{width: containerItem.image_width + 'rpx'}"></image>
							</navigator>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 楼层 -->

	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 1. 轮播图的数据列表，默认为空数组
				swiperList: [],
				// 2. 分类导航的数据列表
				navList: [],
				// 3. 楼层的数据列表
				floorList: [],
			}
		},
		methods: {
			//请求轮播图数据
			async getSwiperList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/swiperdata')
				if (res.meta.status != 200) return uni.$showMsg()
				uni.$showMsg('数据请求成功!')
				this.swiperList = res.message //请求成功，为 data 中的数据赋值
			},

			//请求分类导航区域
			async getNavList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/catitems')
				if (res.meta.status !== 200) return uni.$showMsg()
				uni.$showMsg('获取数据成功！')
				this.navList = res.message
			},

			//获取楼层列表数据的方法
			async getFloorList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/floordata')
				if (res.meta.status !== 200) return uni.$showMsg()
				uni.$showMsg('获取数据成功！')

				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				})
				this.floorList = res.message
			},

			//跳转到分类tabbar页面
			navHandlerChange(item) {
				if (item.name === '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			},
            //搜索页面跳转
            	gotoSearch(){
            		uni.navigateTo({
            			url:'/subpkg/search/search'
            		})
            	}
		},
		onLoad() {
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		}
	}
</script>

<style scoped lang="scss">
	//轮播图的样式
	swiper {
		height: 400rpx;
	}

	.swiper-item,
	image {
		width: 100%;
		height: 100%;
	}


	//分类导航的样式
	.nav-list {
		display: flex;
		justify-content: space-around;
		margin: 20rpx 0;

		.nav-img {
			width: 128rpx;
			height: 140rpx;
		}
	}

	//楼层的样式
	.floor-container-box {
		display: flex;
	}

	.floor-title {
		display: flex;
		height: 120rpx;
		width: 100%;
		margin: 15px 0;
	}

	.container-left {
		height: 300rpx;
	}

	.container-right {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}
	
	.search-box{
		position: sticky;
		top: 0;
		z-index: 999;
	}
</style>
