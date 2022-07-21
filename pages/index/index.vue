<template>
	<!-- <view class="content"> -->
	<!-- 	<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view> -->
	<!-- </view> -->
	<view id="mineSweeper" class="mineSweeper">
		<view class="top">
			<view v-if="hasChoseLevel" class="top-info">
				<view class="top-info__list">
					<view>难度:{{ gameInfo.level }}</view>
					<view>时间:{{ gameInfo.time }}s</view>
				</view>
				<view class="top-info__list">
					<view>剩余:{{ gameInfo.boomNum - flagNum < 0 ? 0 : gameInfo.boomNum - flagNum }}</view>
					<view>最高纪录:{{ gameInfo.record ? `${gameInfo.record}s` : '无' }}</view>
				</view>
			</view>
			<view v-else>请选择难度</view>
		</view>
		<view v-if="!hasChoseLevel">
			<view class="level">
				<view class="level-list" v-for="(item,index) in config" :key="index" @click="choseLevel(item)">
					{{`${item.level} （${item.xNum}×${item.yNum}）`}}
				</view>
			</view>
			<view class="custom">
				<!-- <van-button plain type="info" @click="showOverlay = true">自定义棋盘图片</van-button> -->
			</view>
		</view>

		<view v-else class="game-content">
			<view class="board">
				<view v-for="(row,index) in board" class="board-row">
					<view v-for="(column,key) in row"
						:class="['board-column',gameInfo.yNum <= 9?'board-column--small':gameInfo.yNum < 30?'board-column--middle':'board-column--large']"
						@click="isSetFlag ? setFlag([index,key], column) : isInit ? updateBoard([index,key],column.data) : setBoom([index,key])">
						<view
							:class="['board-column__list',column.isShow ? 'board-column__list--show':'board-column__list--unknown']">
							<image v-if="column.data === 'X'" class="board-column__image" :src="boomimage" alt="炸弹">
								<image v-else-if="column.data === 'F'"
									:class="['board-column__image',!isSetFlag ? 'board-column__image--disable' : '']"
									:src="flagimage" alt="旗帜">
									<!--                        <view v-else-if="parseInt(column.data) > 0">{{ column.data }}</view>-->
									<image v-else-if="parseInt(column.data) > 0"
										class="board-column__image board-column__image--number"
										:src="`static/images/shuzi${column.data}.svg`" :alt="column.data">
						</view>
					</view>
				</view>
			</view>
			<view class="bottom">
				<view class="bottom-button">
					<view v-if="!over"
						:class="['bottom-button__list bottom-button__flag',isSetFlag ? 'bottom-button__flag--active':'']"
						@click="isSetFlag = !isSetFlag">
						插旗
					</view>
					<view class="bottom-button__list bottom-button__restart" @click="handleRestart">重新开始
					</view>
					<view class="bottom-button__list bottom-button__choose" @click="handleRestart(false)">重新选择难度
					</view>
				</view>
				<view v-if="over" class="bottom-tips">踩到地雷，游戏结束</view>
			</view>
		</view>
		<!--  <van-overlay :show="showOverlay" @click="showOverlay = false">
	        <view class="custom-wrapper" @click.stop>
	            <view class="custom-wrapper__upload">
	                <van-uploader class="custom-wrapper__upload-list"
	                              :before-read="beforeRead"
	                              :after-read="(file)=> afterRead(file,'boom')"
	                              @oversize="onOversize">
	                    <van-button icon="plus" type="default">更换地雷图标</van-button>
	                </van-uploader>
	                <van-uploader class="custom-wrapper__upload-list"
	                              :before-read="beforeRead"
	                              :after-read="(file)=> afterRead(file,'flag')"
	                              @oversize="onOversize">
	                    <van-button icon="plus" type="default">更换旗帜图标</van-button>
	                </van-uploader>
	                <van-button class="custom-wrapper__upload-list" icon="replay" type="default" @click="resetimage">还原默认图标
	                </van-button>
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
	                                <image class="board-column__image"
	                                     :src="boomimage"
	                                     alt="炸弹">
	                            </view>
	                        </view>
	                        <view :class="['board-column','board-column--small']">
	                            <view :class="['board-column__list','board-column__list--unknown']">
	                                <image class="board-column__image"
	                                     :src="flagimage"
	                                     alt="旗帜">
	                            </view>
	                        </view>
	                    </view>
	                </view>
	            </view>
	            <view class="custom-wrapper__tips">
	                请上传图片(建议使用白色或透明底的正方形图片)<br/>
	                注意:清除缓存会还原设置的图片
	            </view>
	
	        </view>
	    </van-overlay> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showOverlay: false, // 遮罩层开关
				hasChoseLevel: false, // 难度选择页面开关
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
				// 难度评级配置
				scoreLevel: {
					easy: {
						0.49: '100',
						40: '99',
						70: '88',
						90: '80',
						110: '77',
						180: '66',
						240: '55',
						500: '35',
						800: '15',
						1000: '10',
						1300: '1'
					},
					// 452 293
					middle: {
						7.03: '100',
						250: '99',
						500: '88',
						800: '77',
						1000: '66',
						1200: '55',
						1500: '35',
						2000: '15',
						2500: '10'
					},
					hard: {
						31.13: '100',
						500: '99',
						800: '88',
						1200: '77',
						1800: '66',
						2300: '55',
						3000: '35',
						5000: '15',
						8000: '10'
					}
				},
				// 当前游戏信息
				gameInfo: {
					alias: 'easy',
					level: '青铜', // 难度等级
					time: 0, // 时间
					record: undefined, // 最高纪录
					xNum: 9, // 列数
					yNum: 9, // 行数
					boomNum: 10, // 炸弹数
				},
				flagNum: 0, // 旗帜数
				isInit: false, // 保证第一个点击的格子不是雷
				board: [], // 棋盘
				over: false, // 游戏是否结束
				isSetFlag: false, // 是否插旗状态
				timer: undefined, // 计时器
			}
		},
		computed: {
			// 计算已翻开的格子数 当翻开的格子数=安全的格子数时游戏胜利
			showCount() {
				const {
					board
				} = this;
				const arr = board.flat();
				let num = 0;
				arr.forEach((item) => {
					if (item.isShow) {
						num++
					}
				})
				return num
			},
			// 当前难度的非雷格子数
			saveNum() {
				const {
					gameInfo: {
						xNum,
						yNum,
						boomNum
					}
				} = this;
				return yNum * xNum - boomNum
			},
		},
		watch: {
			// 监听翻开的格子数，判断游戏是否结束
			showCount() {
				const {
					gameInfo: {
						time,
						alias,
						record
					},
					over,
					showCount,
					saveNum,
					scoreLevel
				} = this;
				if (over) return; // 处理最后一个点击到炸弹的异常情况
				if (showCount === saveNum) {
					// 停止计时
					clearInterval(this.timer)

					// 击败玩家百分比
					let percent = '1';

					for (const key in scoreLevel[alias]) {
						console.log(key)

						if (parseFloat(time) < key) {
							percent = scoreLevel[alias][key]
							console.log(key, '是这里', percent)
							break
						}
					}

					// 判断是否为最高纪录
					if (!record || parseFloat(time) < parseFloat(record)) {
						localStorage.setItem(`${alias}_record`, time)
						this.gameInfo.record = time;
					}

					// 弹出对话框
					vant.Dialog({
						message: `恭喜用时${time}秒挑战成功！\n击败了${percent}%的玩家！\n您的最高纪录：${this.gameInfo.record}秒`,
						confirmButtonColor: '#409eff',
						confirmButtonText: '重新开始',
						showCancelButton: true,
						cancelButtonText: '返回主页'
					}).then(() => {
						// 重新开始
						this.restart();
					}).catch(() => {
						// 返回主页
						this.setLevelPage();
					});
				}
			}
		},
		created() {
			// 判断本地缓存中是否已上传自定义图标
			['boom', 'flag'].forEach((item) => {
				if (localStorage.getItem(`${item}_img`)) {
					this[`${item}Img`] = localStorage.getItem(`${item}_img`);
				}
			})
		},
		methods: {
			// 重新开始确认
			handleRestart(isRestart = true) {
				// 停止计时
				clearInterval(this.timer)
				if (this.over) {
					if (isRestart) {
						// 重新开始
						this.restart();
					} else {
						// 返回主页
						this.setLevelPage();
					}
				} else {
					// 弹出对话框
					vant.Dialog({
						message: `确认要重新${isRestart ? '开始' : '选择难度'}吗?`,
						confirmButtonColor: '#409eff',
						confirmButtonText: '确认',
						showCancelButton: true,
						cancelButtonText: '取消'
					}).then(() => {
						if (isRestart) {
							// 重新开始
							this.restart();
						} else {
							// 返回主页
							this.setLevelPage();
						}
					}).catch(() => {
						// 重新计时
						this.timer = setInterval(() => {
							const num = parseFloat(this.gameInfo.time) + 0.01
							this.gameInfo.time = num.toFixed(2)
						}, 10)
					});
				}

			},
			// 还原默认图标
			resetImg() {
				this.boomImg = 'static/images/icon_boom1.svg';
				this.flagImg = 'static/images/flag.svg';
				localStorage.removeItem('boom_img');
				localStorage.removeItem('flag_img');
				vant.Toast('还原成功');
			},
			// 打开难度选择页面
			setLevelPage() {
				clearInterval(this.timer); // 清除定时器
				this.hasChoseLevel = false;
			},
			// 返回布尔值
			beforeRead(file) {
				if (file.type.indexOf('image') === -1) {
					vant.Toast('请上传正确的图片');
					return false;
				}
				return true;
			},
			// 上传回调
			afterRead(files, string) {
				const {
					file
				} = files;
				console.log(files, file)
				// 保存图片到本地缓存
				let canvas = document.createElement('canvas') // 创建Canvas对象(画布)
				let context = canvas.getContext('2d')
				let img = new Image()
				img.src = files.content
				img.onload = () => {

					canvas.width = 100

					canvas.height = 100

					context.drawImage(img, 0, 0, canvas.width, canvas.height)

					if (file.size > 2 * 1024) {
						//如果图片大小大于2M
						vant.Toast.loading({
							message: '正在压缩图片...',
							forbidClick: true,
						});
						files.content = canvas.toDataURL(file.type)
						localStorage.setItem(`${string}_img`, files.content)
						this[`${string}Img`] = files.content;
						vant.Toast('上传成功');
					} else {
						localStorage.setItem(`${string}_img`, files.content)
						this[`${string}Img`] = files.content;
						vant.Toast('上传成功');
					}

				}

			},
			onOversize(file) {
				console.log(file);
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

				this.gameInfo = {
					...this.gameInfo,
					...item,
					record: localStorage.getItem(`${alias}_record`)
				}
				this.hasChoseLevel = true;
				this.restart();
			},

			/**
			 * 插旗
			 *
			 * @param   {Array} click 点击的坐标
			 * @param   {Object} column 点击坐标数据
			 */
			setFlag(click, column) {
				const [cY, cX] = click;
				const {
					data,
					isBoom,
					isShow
				} = column;
				if (isShow) return; // 如果这个格子已经翻开 直接返回
				const obj = {
					data: 'F', // F旗帜
					isShow: false, // 未翻开
					isBoom: isBoom // 炸弹
				};
				if (data === 'F') {
					obj.data = '-1'; // 未翻开的空格子
					this.flagNum -= 1
				} else {
					this.flagNum += 1
				}
				this.editBoard(cX, cY, obj);
			},

			// 开始/重新开始
			restart() {
				const {
					yNum,
					xNum
				} = this.gameInfo;

				this.board = new Array(yNum).fill(new Array(xNum).fill({
					data: '-1',
					isShow: false,
					isBoom: false
				}));
				[this.isInit, this.over, this.isSetFlag, this.gameInfo.time, this.flagNum] = [false, false, false, 0, 0];
				this.timer = setInterval(() => {
					const num = parseFloat(this.gameInfo.time) + 0.01
					this.gameInfo.time = num.toFixed(2)
				}, 10)
			},

			/**
			 * 修改坐标数据
			 *
			 * @param   {String|Number} x   需要修改的x坐标
			 * @param   {String|Number} y   需要修改的y坐标
			 * @param   {Object} data   改变后的数据
			 */
			editBoard(x, y, data) {
				const {
					board
				} = this;
				const row = [...board[y]]; // 获取那一行的数据
				row.splice(x, 1, data) // 修改
				this.$set(board, y, row)
			},

			/**
			 * 初始化棋盘
			 * @return  {Promise}   Promise
			 * @param   {Array} click 点击的坐标
			 *
			 **/
			setBoom(click) {
				const {
					gameInfo: {
						xNum,
						yNum,
						boomNum
					}
				} = this;
				const [cY, cX] = click;
				const dx = [],
					dy = [];
				//生成炸弹
				while (dx.length < boomNum) {
					const randomX = Math.round(Math.random() * (xNum - 1)); //获取一个范围内的随机数
					const randomY = Math.round(Math.random() * (yNum - 1)); //获取一个范围内的随机数
					const isClick = (randomX === parseInt(cX)) && (randomY === parseInt(cY)); // 是否点击的坐标
					if (!isClick && (dx.indexOf(randomX) === -1 || dx.indexOf(randomX) !== dy.indexOf(randomY))) {
						// 如果没有重复且不是点击的坐标则推入
						dx.push(randomX)
						dy.push(randomY)
					}
				}
				// 修改炸弹数据
				for (let i = 0; i < dx.length; i++) {
					// console.log(JSON.stringify([dx[i]][dy[i]]))
					const obj = {
						data: '-1',
						isShow: false,
						isBoom: true //是炸弹
					}

					this.editBoard(dx[i], dy[i], obj);
				}
				this.isInit = true;
				this.updateBoard(click);
			},
			/**
			 * 更新格子状态
			 *
			 * @param   {Array} click 点击的坐标
			 * @param   {Object} data 点击坐标数据值
			 */
			updateBoard(click, data = undefined) {
				const {
					board,
					over,
					gameInfo: {
						xNum,
						yNum
					}
				} = this;
				if (over || data === 'F') return; // 如果游戏已经结束或为旗帜 直接返回
				const dx = [1, -1, 0, 0, -1, 1, -1, 1]; // 横坐标
				const dy = [0, 0, 1, -1, 1, -1, -1, 1]; // 纵坐标
				const inBound = (x, y) => x >= 0 && x < xNum && y >= 0 && y < yNum; // 辅助函数

				const update = (x, y) => {
					if (!inBound(x, y) || board[y][x].isShow || board[y][x].data === 'F') return; // 不在界内或已插旗或已翻开，直接返回
					let count = 0;
					for (let i = 0; i < 8; i++) { // 统计周围雷的个数
						const nX = x + dx[i];
						const nY = y + dy[i];
						if (inBound(nX, nY) && board[nY][nX].isBoom) {
							count++;
						}
					}
					if (count === 0) { // 如果周围没有雷，翻开且标记0，递归
						const obj = {
							data: '0', // 0翻开的空格子
							isShow: true, // 已翻开
							isBoom: false // 不是炸弹
						}
						this.editBoard(x, y, obj)
						for (let i = 0; i < 8; i++) {
							update(x + dx[i], y + dy[i]);
						}
					} else {
						const obj = {
							data: count + '', // 数字1-9附近有炸弹
							isShow: true, // 已翻开
							isBoom: false // 不是炸弹
						}
						this.editBoard(x, y, obj)
					}
				};

				const [cY, cX] = click;
				if (board[cY][cX].isBoom) { // 第一下就踩雷了
					const obj = {
						data: 'X', // X炸弹
						isShow: true, // 已翻开
						isBoom: true // 是炸弹
					};
					this.editBoard(cX, cY, obj);
					this.over = true;
					clearInterval(this.timer)
				} else {
					update(cX, cY); // 开启dfs
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import url("@/static/index.scss");
</style>
