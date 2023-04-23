<template>
	<view>
		<view class="page-body">
			<view class="btn-area">
				<navigator url="/pages/loan/loan">
					<button type="default">月度支出</button>
				</navigator>
				<navigator url="/pages/house/house">
					<button type="default">房款支出</button>
				</navigator>
			</view>
		</view>
	</view>
</template>
<script>
// navigate.vue页面接受参数
export default {
	onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
		console.log(option.id); //打印出上个页面传递的参数。
		console.log(option.name); //打印出上个页面传递的参数。
	}
}
</script>