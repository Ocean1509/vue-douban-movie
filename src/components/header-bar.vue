<template>
	<div class="header-top">
		<span class="left glyphicon glyphicon-chevron-left" v-if="left == 'back'" @click="back"></span>
		<span class="icon-header glyphicon glyphicon-search" aria-hidden="true" v-link="{name:'search'}"></span>
		<h1>{{title}}</h1>
	</div>
	
</template>
<script>
	export default{
		props:['title','left'],
		methods:{
			back(){
				history.back()
			}
		}
	}
</script>
<style scoped>
.header-top{height: 45px;background-color: #2ba8f4;text-align: center;overflow: hidden;position:fixed;top:0;width: 100%;z-index:999;}
h1{font-size: 1.8rem;color:#fff;margin:0;line-height: 45px;}
.icon-header{position: absolute;font-size: 2rem;line-height: 45px;right: 2%;color:#fff;cursor:pointer;}
.left{position: absolute;top:12px;left:12px;color:#fff;font-weight: lighter}
</style>
