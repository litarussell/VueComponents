<template>
	<finder :root="classList"></finder>
</template>

<script>
	import Finder from '../Finder.vue';
	export default{
		data(){
			return {
				classList: [
					{id:1, name:'分类1',
						sub: [
							{id:1, name:'分类1-1',
								sub:[
									{id:1, name:"分类1-1-1"}
								]
							},
							{id:2, name:'分类1-2'}
						]},
					{id:2, name:'分类2',
						sub:[
							{id:1, name:"分类2-1",
								sub:[
									{id:1, name:"分类2-1-1"}
								]
							},
							{id:2, name:"分类2-2"}
						]},
					{id:4, name:'分类3'},
					{id:3, name:'分类4'},
				]
			}
		}
		components: {
			'finder': Finder
		}
	}
</script>