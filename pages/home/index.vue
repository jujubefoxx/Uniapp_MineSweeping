<template>
	<view class="mineSweeper-level">
		<view class="top">
			请选择难度
		</view>
		<view class="level">
			<view class="level-list" v-for="(item,index) in config" :key="index" @click="choseLevel(item)">
				{{`${item.level} （${item.xNum}×${item.yNum}）`}}
			</view>
		</view>
		<button class="btn diy-btn" type="primary" plain="true" @click="goCustom">自定义棋盘图片</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				boomImg: 'static/images/icon_boom1.svg', // 默认地雷图标地址
				flagImg: 'static/images/flag.svg', // 默认旗帜图标地址
				//难度配置
				config: [{
					alias: 'easy',
					level: '青铜',
					xNum: 9, // 列数
					yNum: 9, // 行数
					boomNum: 10, // 炸弹数
				}, {
					alias: 'middle',
					level: '白银',
					xNum: 16,
					yNum: 16,
					boomNum: 40,
				}, {
					alias: 'hard',
					level: '黄金',
					xNum: 16,
					yNum: 30,
					boomNum: 99,
				}],

				flagNum: 0, // 旗帜数
				isInit: false, // 保证第一个点击的格子不是雷
				board: [], // 棋盘
				over: false, // 游戏是否结束
				isSetFlag: false, // 是否插旗状态
				timer: undefined, // 计时器,
				imgUrl: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Ftva1.sinaimg.cn%2Fbmiddle%2F006qir4oly1ge3l1c0nkwj30hg0hrmyb.jpg&refer=http%3A%2F%2Ftva1.sinaimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1668327028&t=d7eec196954f2756c0f06582233d8346'
			}
		},
		onShareAppMessage(res) {
			return {
				title: '来比一比谁扫雷更快！',
				path: '/pages/home/index',
				imageUrl: this.imgUrl
			}
		},
		onShareTimeline(res) {
			return {
				title: '我就是村里扫雷最快的人，不服赛一赛',
				path: '/pages/home/index',
				imageUrl: this.imgUrl
			}
		},
		methods: {
			goCustom() {
				uni.navigateTo({
					url: '/pages/custom/index'
				})
			},
			/**
			 * 选择难度
			 *
			 * @param   {Object} item 选择的难度数据
			 */
			choseLevel(item) {
				const {
					alias
				} = item;
				uni.navigateTo({
					url: '/pages/game/index?level=' + alias
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	@import url("@/static/index.scss");

	.diy-btn {
		margin-top: 20rpx;
		border: 2rpx solid #409eff !important;
		color: #409eff !important;
	}

	//等级
	.level {
		display: flex;
		align-items: center;
		flex-direction: column;
		justify-content: space-around;
		max-width: 360px;
		margin: 10px auto;
		padding: 16px;
		border: 2px dashed #409eff;

		&-list {
			display: inline-block;
			width: 200px;
			height: 30px;
			line-height: 30px;
			margin: 10px;
			white-space: nowrap;
			cursor: pointer;
			-webkit-appearance: none;
			text-align: center;
			box-sizing: border-box;
			outline: none;
			transition: .1s;
			font-weight: 500;
			-moz-user-select: none;
			-webkit-user-select: none;
			-ms-user-select: none;
			font-size: 14px;
			border-radius: 20px;
			color: #fff;
			background-color: #409eff;
			border-color: #409eff;
		}
	}
</style>
