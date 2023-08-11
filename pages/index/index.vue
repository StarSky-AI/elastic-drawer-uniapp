<template>
	<view class="content">
		<elastic-drawer :list="list" :bgWallHeight="250">
			<template #top>
				<view class="elastic-drawer-top-container">
					<text>抽屉上半部分内容区域</text>
				</view>
			</template>
		</elastic-drawer>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: ['A', 'B', 'C', 'D', 'E', 'F', 'G']
			}
		},
		onLoad() {
			
		},
		methods: {

		}
	}
</script>

<style lang="scss">
	.elastic-drawer-top-container {
		padding: 150rpx 0;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
