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

<template>
	<div id="app">
		<router-view></router-view>
	</div>
</template>

<style>
	/*每个页面公共css */
</style>
