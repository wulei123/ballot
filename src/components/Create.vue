<template>
    <div id="create">
        <div class="ticket" id="ticket">
            <h1>创建抽签</h1>
            <form action="#" @submit.prevent="Create()" method="get">
                <label for="num">人数:</label><br><label class="danger" v-show="!verify.num">人数不可为空</label><br>
                <input type="number" name="num" v-model="ticket.num"><br>
                <label for="detail">标题:</label><br><label class="danger" v-show="!verify.detail">标题不可为空</label><br>
                <input type="text" name="detail" v-model="ticket.detail"><br>
                <label for="deadline">截至日期:</label><br><label class="danger"
                                                              v-show="!verify.deadline">截止日期不可为空</label><br>
                <input type="datetime-local" name="deadline" id="deadline" v-model="ticket.deadline"><br><br>
                <button class="button" id="submit">确定</button>
            </form>
        </div>
        <transition name="fade">
            <div class="alert" v-show="toalert">
                <br>
                <h1 v-show="success">发布成功</h1><br>
                <router-link class="button" v-show="success" id="back" to="/index">返回列表</router-link>
                <br><br>
                <button class="button" v-show="success" id="continue" @click="Continue()">继续发布</button>
                <div style="font-size:30px;" v-show="!success">请稍等</div>
            </div>
        </transition>
    </div>
</template>

<script>
import {APIURL} from '../config';
	export default {
		name : 'create',
		data () {
			return {
				ticket : {
					num : '',
					detail : '',
					deadline : ''
				},
				verify : {
					num : true,
					detail : true,
					deadline : true
				},
				toalert : false,
				success : false
			}
		},
		methods : {
			Create : function () {
			    this.CheckAuth();
				if(this.Check()){
					console.log(document.getElementById('deadline').value)
					let deadline = new Date(document.getElementById('deadline').value).getTime();
					console.log(deadline)
					let query = '?num='+this.ticket.num+'&detail='+this.ticket.detail+'&deadline='+deadline;
					this.toalert = true;
					this.$http.get(APIURL+'/ballot'+query).then(response=>{
						if(response.data.id){
							this.success = true;
							console.log('success');
						}
					})
				};
				
			},
			CheckAuth:function (){
				this.$http.get(APIURL+'/ballot/isauth').then(response=>{
        if(response.data==1){
          console.log(response.data+' auth success');
        }else{
        if(!verify_request){
          window.location="https://openapi.yiban.cn/oauth/authorize?client_id="+APPID+"&redirect_uri="+CALLBACK+"&display=html";
        }
        }
    });
			},
			Check : function () {
				if(!this.ticket.num){this.verify.num=false}
				if(!this.ticket.detail){this.verify.detail=false}
				if(!this.ticket.deadline){this.verify.deadline=false}
				if(this.ticket.num&&this.ticket.detail&&this.ticket.deadline){
					return true;
				}else{
					return false;
				}
			},
			Continue : function () {
				this.ticket.num = '';
				this.ticket.detail = '';
				this.ticket.deadline = '';
				this.verify.num = true;
				this.verify.detail = true;
				this.verify.deadline = true;
				this.toalert = false;
				this.success = false;
			}
		}
	}

</script>

<style>
	#create{
		height: 100%;
		width: 100%;
	}
	.button{
		display: block;
		text-decoration: none;
		margin: 0 auto;
	  	transition: all .3s;
		height:50px;
		width:75%;
		background:blue;
		border-radius: 30px;
		color:white;
  		font-family: 'Microsoft YaHei UI';
		font-size: 30px;
		box-shadow: gainsboro 5px 5px 30px;
		outline: none;
		border:none;
	}
	.button:hover{
		background: #00CD00;
	}
	form{
		width: 80%;
		margin: 0 auto;
	}
	form>label{
		font-size: 25px;
	}
	form>input{
		margin-bottom:20px;
		height: 35px;
		font-size: 25px;
		width: 90%;
		border-radius: 18px;
		border: 2px solid blue;
		box-shadow: gainsboro 1px 1px 10px;
		outline: none;
		opacity: .6;
		padding-left: 20px;
	}

	form>input:focus{
		height: 35px;
		border-radius: 18px;
		border: 2px solid blue;
		box-shadow: gainsboro 1px 1px 10px;
		opacity: .8;
	}
	#back{
		padding-top: 10px;
	}
	#back:hover{
		background: blue;
	}
	#continue{
		border:2px solid blue;
		background: white;
		color: blue;
	}
	#continue:hover{
		background: blue;
		color: white;
	}
	label{
		float: left;
	}
	.danger{
		color: red;
	}
	.alert{
		z-index: 10;
		position: fixed;
		height: auto;
		margin: auto;
		padding-bottom: 40px;
		top: 20%;
		left: 0;
		right: 0;
		box-shadow: gainsboro 1px 1px 40px;
		border-radius: 5px;
		background: white;
	}

</style>
