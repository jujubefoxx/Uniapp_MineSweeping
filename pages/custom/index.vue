<template>
	<view id="mineSweeper" class="mineSweeper-custom">
		<view class="custom-wrapper__upload">
			<view class="btn btn-upload" @click="chooseImg('boom')">
				<uni-icons type="plusempty" size="20" color="323233"></uni-icons>更换地雷图标
			</view>
			<view class="btn btn-upload" @click="chooseImg('flag')">
				<uni-icons type="plusempty" size="20" color="323233"></uni-icons>更换旗帜图标
			</view>
			<view class="btn btn-upload" @click="resetImg">
				<uni-icons type="refreshempty" size="20" color="323233"></uni-icons>还原默认图标
			</view>
		</view>

		<view class="custom-wrapper__preview">
			<view class="custom-wrapper__preview-title">预览效果</view>
			<view class="custom-wrapper__preview-board">
				<view class="board-row">
					<view :class="['board-column','board-column--small']">
						<view :class="['board-column__list','board-column__list--unknown']">
						</view>
					</view>
					<view :class="['board-column','board-column--small']">
						<view :class="['board-column__list','board-column__list--show']">
							<image class="board-column__image" :src="boomImg" alt="炸弹" mode="scaleToFill">
						</view>
					</view>
					<view :class="['board-column','board-column--small']">
						<view :class="['board-column__list','board-column__list--unknown']">
							<image class="board-column__image" :src="flagImg" alt="旗帜" mode="scaleToFill">
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="custom-wrapper__tips">
			请上传图片(建议使用白色或透明底的正方形图片)<br />
			注意:删除小程序会还原设置的图片
		</view>

	</view>
	</view>
</template>

<script>
	import {
		pathToBase64,base64ToPath
	} from '@/utils/image-tools/index.js'
	export default {
		data() {
			return {
				boomImg: 'https://s3.bmp.ovh/imgs/2022/10/13/69c118a4fb8897a5.png', // 默认地雷图标地址
				flagImg: 'https://s3.bmp.ovh/imgs/2022/10/13/0f9e276efd4a9e5c.png', // 默认旗帜图标地址
			}
		},
		onLoad() {
			// 判断本地缓存中是否已上传自定义图标
			['boom', 'flag'].forEach((item) => {
				if (uni.getStorageSync(`${item}_img`)) {
					base64ToPath(uni.getStorageSync(`${item}_img`)).then((img)=>{
						this[`${item}Img`] = img; // 转换为链接
					})
				}
			})
		},
		methods: {
			chooseImg(string) {
				const that = this;
				// 需要确认微信是否可用
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					success: function(res) {
						//需要使用uni,getImageInfo获取图片的本地存储路径
						uni.getImageInfo({
							src: res.tempFilePaths[0],
							success: (path) => {
								pathToBase64(path.path).then(base64 => {
										uni.setStorageSync(`${string}_img`, base64)
										that[`${string}Img`] = base64;
									})
									.catch(error => {
										uni.showToast({
											icon: 'none',
											title: '上传图片失败'
										})
									})
							}
						})
					}
				});
			},
			// 还原默认图标
			resetImg() {
				this.boomImg = 'https://s3.bmp.ovh/imgs/2022/10/13/69c118a4fb8897a5.png';
				this.flagImg = 'https://s3.bmp.ovh/imgs/2022/10/13/0f9e276efd4a9e5c.png';
				localStorage.removeItem('boom_img');
				localStorage.removeItem('flag_img');
				uni.showToast({
					icon: 'none',
					title: '还原成功'
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	@import url("@/static/index.scss");

	.btn-upload {
		display: flex;
		align-items: center;
		padding: 0 20rpx;
		height: 80rpx;
		color: #323233;
		background-color: #fff;
		border: 2rpx solid #ebedf0;
		margin-top: 40rpx;

		.uni-icons {
			margin-right: 4rpx;
		}
	}

	// 自定义遮罩层
	.custom {
		text-align: center;
		margin: 20px auto;

		&-wrapper {
			width: 300px;
			margin: 30px auto;
			padding: 20px;
			background: #fff;
			border-radius: 4px;

			&__upload {
				display: flex;
				flex-direction: column;
				justify-content: space-around;
				align-items: center;
				margin: 10px 0;

				&-list {
					margin: 5px 0 !important;
				}
			}

			&__tips {
				margin-top: 30px;
				font-size: 12px;
				color: #606266;
				text-align: center;
			}

			&__preview {
				margin-top: 30px;
				text-align: center;

				&-title {
					margin: 10px;
				}
			}
		}

		&-cropper {
			width: 500px;
			height: 500px;
		}
	}
</style>
