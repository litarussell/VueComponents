<template>
	<div class="finder">
		<!-- 分类层级. -->
		<div class="finder-class"
			:data-level="i" @click="unfoldSub"
			:class="{'finder-sub-add':(i+1==unfold.length)&&(add)}"
			v-for="(items, i) in unfold" :key="i">
			<div class="finder-class-head">
				<span>{{i + 1}}级分类</span>
				<span class="finder-operation finder-add">
					<i class="el-icon-plus finder-class-add"></i></span>
				<span class="finder-operation finder-edit" v-show="items.selected">
					<i class="el-icon-edit finder-class-modify"></i>
				</span>
			</div>
			<ul><li v-for="(item, index) in items.category"
					:class="{'is-active': item.id == items.selected}"
					:data-id="item.id" :key="index">{{item.name}}</li>
			</ul>
		</div>

		<el-dialog title="创建分类" :visible.sync="visible">
		  	<el-container class="add-class">
				<el-upload
					action=""
					class="uploader">
				  	<img v-if="imageUrl" :src="imageUrl" class="pic">
				  	<i v-else class="el-icon-plus uploader-icon"></i>
				</el-upload>
				<el-form :model="form" label-width="80px">
				    <el-form-item label="分类名称">
				      <el-input v-model="form.name" auto-complete="off"></el-input>
				    </el-form-item>
				    <el-form-item label="属性选择">
				      	<el-select v-model="form.region"
				      		placeholder="请选择属性" multiple>
				        	<el-option v-for="(item,index) in options"
				        	:label="item.label"
				        	:value="item.value"
				        	:key="index"></el-option>
				      </el-select>
				    </el-form-item>
			  	</el-form>
			</el-container>
		  	<div slot="footer" class="dialog-footer">
			    <el-button @click="visible = false">取消</el-button>
			    <el-button type="primary" @click="visible = false">立即创建</el-button>
			</div>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		name: "Finder",
		props: ['root'],
		data(){
			return {
				unfold: [ { category: this.root, selected: 0 }, ],
				data: "",
				options:[{value:1,label:'选项一'}, {value:2,label:'选项二'}],
				add: false,
				visible: false,
				imageUrl:"",
				form: {},
			}
		},
		methods: {
			sub(level,id){
				// 选中的父类
				let father = this.unfold[level].category.filter(function(item){
					return item.id == id
				})[0];
				// 设置选中
				this.unfold[level].selected = id;
				console.log("选择的分类id: ", id);
				console.log("选中的父类", father);
				if(father.sub){
					console.log("该分类下有子类",father.sub);
					this.unfold.push({
						category: father.sub,
						selected: 0
					});
					this.add = false;
				}else{
					console.log("该分类下没有子分类");
					this.add = true;
				}
			},
			// 处理点击到列表的事件
			list(level, id){
				console.log("-------------start------------");
				console.log("当前层级: " + level);
				console.log(this.unfold);

				// 当前选择同上次相同
				if (id == this.unfold[level].selected) {
					console.log("当前选择同上次相同");
					console.log("---------------end---------------");
					return;
				}
				// 若点击当前展开分类的子分类,即最后叶子分类
				if(level == this.unfold.length - 1) {
					// 查找该层级下的分类
					this.sub(level,id);
				// 当前点击的层级小于已展开的最高非叶层级
				}else if(level < this.unfold.length - 1){
					// 删除滞后的展开子分类
					this.unfold.splice(level+1, this.unfold.length - level - 1);
					this.sub(level,id);
				}
				console.log("变化后层级: ", level);
				console.log("当前展开层级",this.unfold);
				console.log("---------------end--------------");
			},
			// 事件委托
			unfoldSub(event){
				let target = event.target, 				 //目标元素
					currentTarget = event.currentTarget, //当前元素
					level = Number(currentTarget.dataset.level); //点击的分类层级
				// 如果
				if(target.dataset.id){
					this.list(level, target.dataset.id);
				}else{
					console.log("点击的不是列表", target);
					if (target.classList.contains('finder-sub-add')) {
						this.visible = true;
					}
					if (target.classList.contains('finder-class-add')) {
						this.visible = true;
					}
					if (target.classList.contains('finder-class-modify')) {
						this.visible = true;
					}
				}
			},
		},
		computed:{
			sort(){
				return this.data.sort( (a,b)=>{
					if(a.id < b.id){
						return -1;
					}else if(a.id > b.id){
						return 1;
					}else{
						return 0;
					}
				});
			},
		},
	}
</script>

<style>
	.finder{
		display: flex;
		min-width: 100px;
	}
	.finder-class{
		position: relative;
		display: inline-block;
		border: 1px solid #ebebeb;
		border-left: none;
		min-width: 150px;
	}
	.finder-sub-add:after{
		cursor: pointer;
		font-family: element-icons;
	    content: "\e62b";
	    font-size: 40px;
	    color: #bfcbd9;
	    position: absolute;
	    right: 0; top: 50%;
	    transform: translate(150%, -50%);
	}
	.finder-class:first-child{
		border-left: 1px solid #ebebeb;
	}
	.finder-class-head{
		display: flex;
		justify-content: space-between;
		font-size: 20px;
		padding: 14px 20px;
	}
	.finder ul{
		list-style: none;
		padding: 0; margin: 0;
	}
	.finder li{
		padding: 10px 20px;
		cursor: pointer;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.finder li.is-active{
		color: #409eff;
	}
	.finder li:after{
		font-family: element-icons;
	    content: "\E604";
	    font-size: 14px;
	    color: #bfcbd9;
	}
	.finder-operation{
		cursor: pointer;
		margin: 0 4px;
	}
	.add-class{
		display: flex;
  		justify-content: center;
	}
</style>