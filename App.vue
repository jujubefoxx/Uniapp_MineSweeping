<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/*每个页面公共css */
	.board {
		max-width: 740rpx;
		background: whitesmoke;
		padding: 10px;
		margin: 10px auto;

		&-row {
			display: flex;
			overflow: hidden;
			justify-content: center;
		}

		&-column {
			display: flex;
			justify-content: center;
			align-items: center;
			//flex: 1;
			margin: 1px;
			background: gainsboro;
			overflow: hidden;

			&--small {
				width: 38px;
				height: 38px;
			}

			&--middle {
				width: 20px;
				height: 20px;
				font-size: 12px;
			}

			&--large {
				width: 20px;
				height: 20px;
				font-size: 12px;
			}

			&__list {
				display: flex;
				justify-content: center;
				align-items: center;
				width: 100%;
				height: 100%;
				overflow: hidden;

				&--unknown {
					cursor: pointer;
				}

				&--show {
					box-sizing: border-box;
					background: #fff;
					box-shadow: gainsboro 2px 2px inset;
					padding: 2px 0 0 2px;
				}
			}

			&__image {
				width: 85%;
				height: 85%;

				&--disable {
					cursor: auto;
				}
			}
		}
	}
</style>
