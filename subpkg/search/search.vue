<template>
	<view>
		<uni-search-bar ref="mySearchBar" @confirm="search" :focus="true" v-model="kw" @input="input"
			cancelButton="none" @cancel="cancel" @clear="clear">
		</uni-search-bar>
		<!-- 搜索建议列表 -->
		<view class="sugg-list" v-if="searchResults.length != 0 ">
			<view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>

		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="clearHistory"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item, i) in historyList" :key="i"  @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer: null, //定时器
				kw: '', //存储搜索框的关键词
				searchResults: [], // 搜索结果列表
				historyList: [] // 搜索关键词的历史记录
			};
		},
		methods: {
			input(e) {
				clearTimeout(this.timer)
				this.timer = setTimeout(() => {
					this.kw = e
					this.getSearchList()
				}, 500)
			},

			//获取搜索关键词的数据
			async getSearchList() {
				// 判断关键词是否为空
				if (this.kw === '') {
					this.searchResults = []
					return
				}
				// 发起请求，获取搜索建议列表
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/qsearch', {
					query: this.kw
				})
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				this.saveSearchHistory() //保存搜索关键词
			},

			//点击进入商品详情页
			gotoDetail(goods_id) {
				uni.navigateTo({
					// 指定详情页面的 URL 地址，并传递 goods_id 参数
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
				})
			},
            
			// 点击跳转到商品列表页面
			gotoGoodsList(kw) {
			  uni.navigateTo({
			    url: '/subpkg/goods_list/goods_list?query=' + kw
			  })
			},
			
			//保存搜索关键词的方法
			saveSearchHistory() {
				//直接把搜索关键词 push 到 historyList 数组中
				this.historyList=this.historyList.filter(item=>{
					return item != this.kw
				})
				this.historyList.unshift(this.kw)
				uni.setStorageSync('kw', JSON.stringify(this.historyList)) //将搜索记录存储到本地存储中
			},
			
			//清空搜素记录
			clearHistory() {
			  // 清空 data 中保存的搜索历史
			  this.historyList = []
			  // 清空本地存储中记录的搜索历史
			  uni.removeStorageSync('kw')
			}
		},
		onLoad() {
			 this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		}
	}
</script>

<style lang="scss">
	.sugg-list {
		padding: 0 10px;

		.sugg-item {
			display: flex;
			padding: 15px 0;
			justify-content: space-between;

			.goods-name {
				// 文字不允许换行（单行文本）
				white-space: nowrap;
				// 溢出部分隐藏
				overflow: hidden;
				// 文本溢出后，使用 ... 代替
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}
	}

	.history-box {
		padding: 0 15px;

		.history-title {
			display: flex;
			justify-content: space-between;
			align-items: center;
			border-bottom: 1px solid #efefef;
			height: 40px;
		}
	}

	.history-list {
		display: flex;
		flex-wrap: wrap;

		uni-tag {
			margin-top: 10px;
			margin-right: 15px;
		}
	}
</style>
