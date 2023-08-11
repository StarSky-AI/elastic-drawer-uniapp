<template>
	<view class="elastic-drawer">
		<!-- #ifndef APP-NVUE || APP-PLUS-NVUE -->
		<scroll-view class="elastic-drawer-content" :style="{width: winW, height: winH}" scroll-y
			:show-scrollbar="false" scroll-anchoring scroll-with-animation refresher-enabled refresher-default-style="none"
			:refresher-threshold="0" refresher-background="transparent" :refresher-triggered="refresherTriggered"
			@refresherpulling="refresherpulling" @refresherrefresh="refresherrefresh"
			@refresherrestore="refresherrestore" @refresherabort="refresherabort">
			<view>
				<view class="elastic-drawer-bg" @click="clickBackground" :style="{height: bgWallHeight + 'px'}">
					<image class="elastic-drawer-bg-img" :src="bgUrl" :animation="animationData"
						:style="{height: bgWallHeight + 'px'}"></image>
				</view>
				<!-- 这里是用来盛放抽屉的上半部分数据 -->
				<view class="elastic-drawer-content-top" v-if="isShowCard"
					:style="{borderTopLeftRadius: radiusSize + 'px', borderTopRightRadius: radiusSize + 'px', height: !isShowSticky && !isShowList ? drawerTopH : 'auto', marginTop: -radiusSize + 'px'}">
					<slot name="top"></slot>
				</view>
				<!-- 吸顶tabs -->
				<view class="sticky-tabs" v-if="isShowSticky">
					<view v-for="num in list">
						<text>{{num}}</text>
					</view>
				</view>
				<view class="elastic-drawer-content-bottom" v-for="num in list" v-if="isShowList">
					<!-- 这里是用来盛放抽屉的下半部分列表数据 -->
					<view class="elastic-drawer-content-bottom-item">
						<text>{{num+'列表内容项'}}</text>
					</view>
				</view>
			</view>
		</scroll-view>
		<!-- #endif -->
		<!-- #ifdef APP-NVUE || APP-PLUS-NVUE -->
		<view class="elastic-drawer-bg" ref="headerBg" :style="{height: bgWallHeight + 'px'}" @click="clickBackground">
			<image class="elastic-drawer-bg-img" :src="bgUrl" :fade-show="false" :style="{height: bgWallHeight + 'px'}">
			</image>
		</view>

		<list class="elastic-drawer-content" :style="{width: winW, height: winH}" @onRefresh="onrefresh" ref="list"
			fixFreezing="true" :show-scrollbar="false">
			<refresh style="width: 750rpx;height: 0;" v-if="isAndroid" @refresh="onrefresh"
				:display="refreshing ? 'show' : 'hide'">
			</refresh>
			<cell class="elastic-drawer-bg-reserve" :style="{height: (bgWallHeight - radiusSize) + 'px'}"
				@click="clickBackground">
				<!-- 用来盛放背景墙区域的内容 -->
			</cell>
			<cell v-if="isShowCard" class="elastic-drawer-content-top"
				:style="{borderTopLeftRadius: radiusSize + 'px', borderTopRightRadius: radiusSize + 'px'}">
				<!-- 这里是用来盛放抽屉的上半部分数据 -->
				<slot name="top"></slot>
			</cell>
			<header v-if="isShowSticky">
				<!-- 吸顶tabs -->
				<view class="sticky-tabs">
					<view v-for="num in list">
						<text>{{num}}</text>
					</view>
				</view>
			</header>
			<cell class="elastic-drawer-content-bottom" v-for="num in list" v-if="isShowList">
				<!-- 这里是用来盛放抽屉的下半部分列表数据 -->
				<view class="elastic-drawer-content-bottom-item">
					<text>{{num+'列表项内容'}}</text>
				</view>
			</cell>
		</list>
		<!-- #endif -->
	</view>
</template>

<script>
	// #ifdef APP-NVUE || APP-PLUS-NVUE
	const binding = uni.requireNativePlugin('bindingx');
	// #endif
	export default {
		props: {
			list: {
				type: Array,
				default: () => {
					return [];
				}
			},
			//背景墙高度
			bgWallHeight: {
				type: [String, Number],
				default: "250"
			},
			/* 是否显示抽屉的上半部分内容 */
			isShowCard: {
				type: Boolean,
				default: true
			},
			/* 设置抽屉圆角尺寸 */
			radiusSize: {
				type: [String, Number],
				default: "15"
			},
			/* 是否显示tabs选项卡 */
			isShowSticky: {
				type: Boolean,
				default: true
			},
			/* 是否显示tabs选项卡 */
			isShowList: {
				type: Boolean,
				default: true
			},
			/* 背景墙图片 */
			bgUrl: {
				type: String,
				default: 'https://pic.616pic.com/bg_w1180/00/01/54/j2vo5zc0Jt.jpg'
			}
		},
		data() {
			return {
				winH: 0,
				winW: 0,
				refreshing: false,
				isAndroid: uni.getSystemInfoSync().platform == "android",
				animationData: {},
				refresherTriggered: false, //下拉刷新状态
				_refresherTriggered: false, //防止异步操作
				drawerTopH: 0
			}
		},
		mounted() {
			this.winW = uni.getSystemInfoSync().windowWidth + 'px';
			this.winH = (uni.getSystemInfoSync().windowHeight) + 'px';
			this.drawerTopH = uni.getSystemInfoSync().windowHeight - (this.bgWallHeight - this.radiusSize) + 'px';
			// #ifdef APP-NVUE || APP-PLUS-NVUE
			setTimeout(() => {
				this.headerBgBinding();
			}, 100)
			// #endif
		},
		beforeDestroy() {
			// #ifdef APP-NVUE || APP-PLUS-NVUE
			this.headerBgBindingDestory();
			// #endif
		},
		methods: {
			// #ifdef APP-NVUE || APP-PLUS-NVUE
			async onrefresh() {
				this.refreshing = true;

				setTimeout(() => {
					this.refreshing = false;
				}, 1000)
			},
			// #endif
			headerBgBinding() {
				// #ifdef APP-NVUE || APP-PLUS-NVUE
				let self = this,
					scroller = self.$refs.list.ref,
					headerBg = self.$refs.headerBg.ref;
				let bindingResult = binding && binding.bind({
					eventType: 'scroll',
					anchor: scroller,
					props: [{
							element: headerBg,
							property: 'transform.scale',
							expression: {
								origin: 'y<0?(1-y/80):1' //下拉放大
							}
						},
						{
							element: headerBg, //动画元素
							property: 'transform.translateY', //动画属性
							expression: 'y * -1'
						},

					]
				}, function(e) {});
				self.gesToken = bindingResult.token;
				console.log('bindongx', scroller, headerBg, self.gesToken)
				// #endif
			},
			headerBgBindingDestory() {
				// #ifdef APP-NVUE ||APP-PLUS-NVUE
				let self = this;
				if (self.gesToken != 0) {
					binding.unbind({
						eventType: 'scroll',
						token: self.gesToken
					})
					self.gesToken = 0;
				}
				// #endif
			},
			clickBackground() {
				uni.showToast({
					title: '您点击了背景墙',
					icon: 'none'
				});
			},

			refresherpulling(e) {
				// #ifndef APP-NVUE || APP-PLUS-NVUE
				let _this = this;
				let y = e.detail.deltaY;
				var animation = uni.createAnimation({
					duration: 0,
					timingFunction: 'ease-in',
				})
				_this.animation = animation
				if (y > 0) {
					animation.scale(y / 80 + 1).step()

					_this.animationData = animation.export()

				} else {
					return false;
				}
				// #endif
			},
			refresherrefresh(e) {
				// #ifndef APP-NVUE || APP-PLUS-NVUE
				let _this = this;
				if (_this._refresherTriggered) {
					return;
				}
				_this._refresherTriggered = true;
				//界面下拉触发，triggered可能不是true，要设为true
				if (!_this.refresherTriggered) {
					_this.refresherTriggered = true;
				}
				_this.loadStoreData(e);
				// #endif
			},
			refresherrestore() {
				// #ifndef APP-NVUE || APP-PLUS-NVUE
				let _this = this;
				_this.refresherTriggered = false;
				_this._refresherTriggered = false;
				// #endif
			},
			refresherabort() {
				// #ifndef APP-NVUE || APP-PLUS-NVUE
				let _this = this;
				_this.refresherTriggered = false;
				_this._refresherTriggered = false;
				// #endif
			},
			loadStoreData(e) {
				// #ifndef APP-NVUE || APP-PLUS-NVUE
				let _this = this;
				setTimeout(() => {
					_this.refresherTriggered = false; //下拉复位
					_this._refresherTriggered = false;
					var animation = uni.createAnimation({
						duration: 0,
						timingFunction: 'ease-out',
					})
					_this.animation = animation
					animation.scale(1).step()
					_this.animationData = animation.export()
				}, 20);
				// #endif
			}
		}
	}
</script>

<style lang="less" scoped>
	/* #ifndef APP-NVUE || APP-PLUS-NVUE */
	uni-image {
		width: 100%;
	}

	.elastic-drawer-content ::-webkit-scrollbar {
		width: 0 !important;
		height: 0 !important;
		color: transparent !important;
		display: none;
	}

	/* #endif */

	.elastic-drawer {
		/* #ifndef APP-NVUE || APP-PLUS-NVUE */
		width: 100%;
		overflow: hidden;
		display: flex;
		/* #endif */
		position: relative;
		background-color: #fff;
		flex-direction: column;

		&-bg {
			width: 750rpx;
			/* #ifndef APP-NVUE || APP-PLUS-NVUE */
			width: 100%;
			position: relative;
			/* #endif */
			display: flex;
			/* #ifdef APP-NVUE || APP-PLUS-NVUE */
			position: absolute;
			left: 0;
			right: 0;
			bottom: 0;
			top: 0;

			/* #endif */
			&-img {
				width: 750rpx;
				/* #ifndef APP-NVUE || APP-PLUS-NVUE */
				width: 100%;
				position: absolute;
				top: 0;
				left: 0;
				right: 0;
				bottom: 0;
				/* #endif */
			}

			&-reserve {
				width: 750rpx;
				background-color: transparent;
			}
		}

		&-content {
			width: 750rpx;
			/* #ifndef APP-NVUE || APP-PLUS-NVUE */
			width: 100%;
			/* #endif */
			background-color: transparent;
			&-top {
				position: relative;
				width: 750rpx;
				/* #ifndef APP-NVUE || APP-PLUS-NVUE */
				width: 100%;
				/* #endif */
				background-color: #fff;
			}

			&-bottom {
				width: 750rpx;
				position: relative;
				/* #ifndef APP-NVUE || APP-PLUS-NVUE */
				display: flex;
				width: 100%;
				/* #endif */
				height: 250px;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				border-bottom: 1px solid #ddd;
				background-color: #ffffff;
			}
		}
	}

	.sticky-tabs {
		width: 750rpx;
		background-color: #007aff;
		height: 100rpx;
		position: sticky;
		/* #ifndef APP-NVUE || APP-PLUS-NVUE */
		width: 100%;
		display: flex;
		position: -webkit-sticky;
		z-index: 999999;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		padding: 0 30rpx;
		top: 0;
		left: 0;
		right: 0;
		transition-property: background-color;
		transition-duration: 3000ms;
		transition-timing-function: linear;
		transition-delay: 0s;
	}
</style>