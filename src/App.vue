<template>
	<div id="app" :class="isChecked ? 'true' : 'false'" ref="appRef">
		     <h1 :class="isChecked ? 'true1' : 'false1'">My Tasks Professional</h1>
			 <a-switch :checked="isChecked" @click="onChange" class="switch" size="small" title="主题切换"/>
			 <a-avatar src="https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png" class="touxiang"/>

			 <a-input placeholder="请输入任务" class="my_ipt" :value="inputValue" @change="handleInputChange"/>
			 <a-button type="primary" @click="addItemToList">添加事项</a-button>
			 
			 <!-- 操作按钮 -->
			 <a-button-group class="btn">
			 	<a-button :type="viewKey === 'all' ? 'primary' : 'default'" @click="changeList('all')">全部</a-button>
				<a-button :type="viewKey === 'done' ? 'primary' : 'default'" @click="changeList('done')">已完成</a-button>
				 <a-badge :count="unDoneLength" title="未完成项">
				     <a-button :type="viewKey === 'undone' ? 'primary' : 'default'" @click="changeList('undone')">未完成</a-button>
				    </a-badge>
			 </a-button-group>
			 
			 <a-list bordered :dataSource="infoList" class="dt_list" >
			 	<a-list-item slot="renderItem" slot-scope="item" class="dt_list_item">
			 		<!-- 复选框 -->
			 		<a-checkbox :checked="item.done" @change="(e) => {cbStatusChanged(e,item.id)}">{{item.info}}</a-checkbox>
			 		<!-- 删除链接 -->
					 <a-button shape="circle" icon="delete" size="small" type="primary" @click="removeItemById(item.id)"/>
			 	</a-list-item>
			 
			 	<!-- footer区域 -->
			 	<div slot="footer" class="footer">
			 		<!-- 未完成的任务个数 -->
			 		<span><a-badge status="processing" />待完成任务 <b>{{unDoneLength}}</b> 项 </span>
					
					<!-- 把已经完成的任务清空 -->
					<a @click="clean">清除已完成项</a> 
					
			 	</div>
			 </a-list>
			  <a-slider id="test" :default-value="100" @change="slide" vertical title="透明度改变"/>
	</div>
</template>

<script>
	import {mapState,mapGetters} from 'vuex'
	import '@/assets/container.css'
	export default {
		name: 'app',
		data() {
			return {
				isChecked:false,
				value:1
			}
		},
		created(){
			this.$store.dispatch('getList')
		},
		computed:{
			...mapState(['inputValue','viewKey']),
			...mapGetters(['unDoneLength','infoList'])
		},
		methods:{
			//监听文本框内容变化
			handleInputChange(e){
				this.$store.commit('setInputValue',e.target.value)
			},
			//向列表中新增 item 项
			addItemToList(){
				if(this.inputValue.trim().length <= 0){			
				return this.$message.warning('文本框内容不能为空!')	
				}
				this.$store.commit('addItem')
			},
			//根据Id删除对应的任务事项
			removeItemById(id){
				this.$store.commit('removeItem',id)
			},
			//监听复选框选中状态变化的事件
			cbStatusChanged(e,id){
				//通过e.target.checked可以接收到最新的选中状态
				const param = {
					id:id,
					status:e.target.checked
				}
				this.$store.commit('changeStatus',param)
			},
			//清除已完成的任务
			clean(){
				this.$store.commit('cleanDone')
			},
			//修改页面上展示的列表数据
			changeList(key){
				this.$store.commit('changeViewKey',key)
			},
			onChange(){
				this.isChecked = !this.isChecked;
			},
			slide(value){
				this.$refs.appRef.style.opacity = value/100;
			}
		}
	}
</script>

<style scoped>
	#app {
		padding: 10px;
		min-height:600px;
		width:870px;
		border:2px solid rgba(255,0,0,.2);
		border-radius: 10px;
		padding:10px 30px 30px 30px;
		background-color:rgba(255,255,255,.4);
		position:absolute;
		top:50%;
		left:50%;
		transform: translate(-50%,-50%);
		position:relative;
	}
	#test{
		position:absolute;
		top:0;
		right:-50px;
	}
	.true{
		background-color:rgba(255,0,0,.4) !important;
	}
	.false{
		background-color:rgba(255,255,255,.4) !important;
	}
	.true1{
		color:rgba(255,255,255,.8) !important;
	}
	.false1{
		color:rgba(0,0,0,.5) !important;
	}
    .touxiang{
		position:absolute;
		top:12px;
		left:255px;
		cursor:pointer;
	}
	.switch{
		position:absolute;
		top:12px;
		right:20px;
	}
	h1{
		margin-bottom:30px;
		padding:0;
		height:40px;
		text-align:center; 
		color:rgba(0,0,0,.5);
		font-size:26px;
		text-shadow: 3px 5px 5px #f60;
	}
	.my_ipt {
		width: 700px;
		margin-right: 10px;
	}

	.dt_list {
		width: 700px;
		background-color:rgba(255,255,255,.8);
	}
	
	.dt_list_item{
		border-bottom:1px solid #999 !important;
		margin:5px 0 20px 0;
	}
	
	.btn{
		margin:25px 0 15px 0;
	}

	.footer {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
</style>
