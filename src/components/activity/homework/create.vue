<style scoped lang="less">
	@import "../../../assets/reset-element-ui/form.css";
	@import "../../../assets/less/icon.less";
	@import "../../../assets/less/activity.less";

	.avatar-uploader {
		.avatar {
			display: inline-block;
			background-color: #fbfdff;
			border: 1px dashed #d9d9d9;
			border-radius: 6px;
      // cursor: pointer;
      position: relative;
      overflow: hidden;
      text-align: center;

  }
  .el-icon-plus {
  	margin-top: 40px;
  	margin-top: 34px;
  	font-size: 18px;
  	color: #999;
  }
  .avatar {
  	width: 88px;
  	height: 88px;
  }
  .sel2,
  .sel1 {
  	position: absolute;
  	display: inline-block;
  	width: 108px;
  	height: 30px;
  	background-color: #f2fbfa;
  	border:1px solid #82cac3;
  	color: #333;
  	font-size: 14px;
  	line-height: 30px;
  	text-align: center;
  	border-radius: 3px;
  }
  .sel1 {
  	opacity: 0;
  }
  .sel2 {
  	margin-left: 20px;
  	>i{
  		font-size: 12px;
  	}
  }
}
.avatar-uploader img{
	margin-right: 10px;
	margin-bottom: 10px;
}
.avatar-uploader li{
	float:left;
	position: relative;
}
.avatar-uploader .error{
	position: absolute;
	right: 0px;
	top: -5px;
}
.dialog-form .selectStudent{
	border: 1px solid #bfcbd9;
	padding:10px 5px 10px 20px;
	margin-top: 10px;
	border-radius: 3px;
	max-height:150px ;
	overflow-y:auto;
}
.dialog-form .selectStudent li{
	float: left;
	line-height: 25px;
	color: #5d6a7c;
	padding-right: 15px;
}
.el-transfer-panel .el-transfer-panel__header{
	color: #35a69d!important;
}
.word{	
	margin-left: 120px;
	float: left;
	position: relative;
	width: 300px;
	font-size: 14px;
	color: #c0c4cc;
}

#sel2{
	    position: absolute;
    display: inline-block;
    width: 108px;
    height: 30px;
    background-color: #f2fbfa;
    border: 1px solid #82cac3;
    color: #333;
    font-size: 14px;
    line-height: 30px;
    text-align: center;
	border-radius: 3px;
	margin-left: 0px;
}

</style>
<template>
	<div id="wcreateWork">
		<el-dialog class="dialog-form" title="新建作业"
			:visible.sync="dialogAddWork"
			:close-on-click-modal="false"
			:before-close="close"
			:lock-scroll="true"
			@open="open">

			<el-form :model="ruleForm" :rules="rules" ref="ruleForm"
						v-loading="loading"
						element-loading-text="拼命加载中">

				<el-form-item label="作业标题：" prop="title" :label-width="labelWidth">
					<el-input v-model.trim="ruleForm.title" placeholder="30字以内"></el-input>
				</el-form-item>
				<el-form-item label="作业说明：" prop="content" :label-width="labelWidth">
					<el-input type="textarea" v-model="ruleForm.content" placeholder="200字以内(选填)"></el-input>
				</el-form-item>

				<el-form-item :label-width="labelWidth"  prop="photo" class="avatar-uploader">
					<label for="fileName" class="sel2"><span class="icon-dis-add"></span>添加照片</label>
					<input class="sel1 select-file" multiple="true" id="fileName" type="file" name="ruleForm.photo" accept="image/png,image/gif,image/jpeg"  @change="selAvatar($event)">

					<div style="margin-top:40px;margin-left:10px;">
						<ul v-if = "imageUrl.length != ''">
							<li v-for="(item,index) in imageUrl" :key="index">
								<img :src="item" class="avatar" style="width:150px;height: 150px;">
								<span class="icon-dis-error error" @click="deletePic(index,$event)"></span>
							</li>
						</ul>
					</div>
				</el-form-item>

				<el-form-item label="所属课程：" prop="course" :label-width="labelWidth">
					<p>{{taskName}}</p>
				</el-form-item>

				<!-- 开放方式 -->
				<el-form-item label="开放方式：" :label-width="labelWidth" prop="type">
					<el-radio-group v-model="ruleForm.openmode">
						<el-radio label='TIMING'>定时开放</el-radio>
						<el-radio label='MANUAL'>手动开放</el-radio>
					</el-radio-group>
				</el-form-item>
				
				<template v-if="ruleForm.openmode == 'TIMING'">
					<el-form-item label="开始日期：" :label-width="labelWidth">
						<el-date-picker
							v-model="ruleForm.beginTime"
							type="date"
							placeholder="立即开始"
							:picker-options="pickerOptions0">
						</el-date-picker>
					</el-form-item>
					<el-form-item label="截止日期：" :label-width="labelWidth">
							<el-date-picker
							v-model="ruleForm.endTime"
							type="date"
							placeholder="永久"
							:picker-options="pickerOptions1">
						</el-date-picker>
					</el-form-item>
				</template>
				
				<el-form-item label="参与人：" prop="members" :label-width="labelWidth">
					<el-select v-model="ruleForm.members" placeholder="全体成员" @change="membersChange">
						<el-option label="全体成员" value="all"></el-option>
						<el-option label="指定参与人员" value="sel"></el-option>
						<el-option label="指定分组成员" value="groupSel" v-if="canSelectGroup"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="选择成员：" prop="" v-show="specifyStudent" :label-width="labelWidth">
					<a href="javascript:void(0)" class="icon-dis-choose" @click="studentSelect = true">点击选择成员</a>
					<div class="selectStudent" v-if="selectData.length != 0">
						<ul class="clearfix" >
							<li v-for="(item,index) in selectData" :key="index">{{item.name}}（{{item.id}}）</li>
						</ul>
					</div>
				</el-form-item>


				<!--新增作业附件-->
				<open-upload
					:fileNum = "fileNum" 
					ref="openUploads" 
					:fileLists = "fileLists" 
					@fileList = "fileListAll">
					
				</open-upload>
				
			</el-form>
			<div slot="footer" class="dialog-footer">
		        <el-button type="primary" @click="!loading && submitForm('ruleForm')">发布</el-button>
		        <el-button @click="close">关闭</el-button>
		    </div>
		</el-dialog>

		<!--选择学员_start-->
	    <student-select
	      	:dialogStudentSelect.sync = "studentSelect"
	     	@StudentSelectData="selectDataMthod"
			@getGroupUser="getgroupUserList"
        	:groupTitles = "groupTitle"
        	:empty = "empty"
			:selectGroups="selectGroups"
	    ></student-select>
	    <!--选择学员_end-->

		<!-- 存储空间不足弹框 -->
		<overLimitPop 
			:overLimitDialog.sync = "overLimit" 
			:text = "'您所属机构的存储空间已达上限，新增培训数据无法存储'" 
			:footerText = "'增加存储空间'" 
			:param="'storage'"></overLimitPop>
	</div>
</template>
<script>
	import work from 'model/activity/homework'
	import studentSelect from 'components/activity/discuss/student_select'
  	import bus from '@/global/bus'
  	import { dateFormatStr,strFormatDate } from '@/global/open'
  	import { getCurRole } from '@/filters/userVerify'
	  import openUpload from '@/base/upload/upload'
	  import overLimitPop from "@/base/overLimitPop/index"

	export default {
		data() {
			return {
				pickerOptions0: {
					disabledDate(time) {
						return time.getTime() < Date.now() - 8.64e7;
					}
				},
				pickerOptions1: {
					disabledDate(time) {
						return time.getTime() < Date.now() - 8.64e7;
					}
				},
				imageUrl: [],
                groupTitle:["学员列表","已选学员列表"],
				scoreShow: false,			// 是否显示打分
				num:0,
				selectData:[],
				specifyStudent: false,		// 是否制定参与人
				ids:[],
				expertsName: '',
				expertsId: '',
				ruleForm: {
					title: '',
					content: '',
					photo: [],
					course: '',
					beginTime: '',
					endTime: '',
					members: 'all',
					openmode: 'TIMING'

				},
				selectGroups:false,  //选择分组 or 选择指定学员
				labelWidth: "130px",
				rules: {
					title: [
						{required: true, message: '请输入作业标题', trigger: 'blur'},
						{max: 30, message: '作业标题请输入30字以内', trigger: 'blur'}
						],
					content: [
						{ required: false, max:200, message: '作业说明请输入200字以内', trigger: 'change' }
						]
				},
				studentSelect:false,
				empty: false,			// 关闭创建，穿梭框清空
				loading: false,
				file:"",
				clazzId:"",
				courseId2:"",
				activityId:"",
				uploadimg:"",
				crumb:this.taskName,

				//上传附件
				fileLists:[],
				fileNum:0,
				canSelectGroup:false,	//分组成员是否可见
				overLimit: false, 		// 存储空间弹框
			}
		
		
		},
		computed: {
			role() {
				return getCurRole();
			}
		},
		created(){
			this.getGroupList();
		},
		props:["dialogAddWork","taskName","courseId", "defaultFlag"],
		methods: {
			open() {
				this.loading = false;
				this.num=0;
				this.selectData=[];
				this.specifyStudent=false;		// 是否制定参与人
				this.ids=[];
				this.expertsName='';
				this.expertsId= '';

				this.ruleForm.beginTime = "";
				this.ruleForm.endTime = "";

				

			},
			resetAttach(){
				this.fileLists = [];
				this.fileNum = 0;
				this.$refs.openUploads.resetVal();
			},
			fileListAll(val){
				//console.log(typeof val);
				this.fileLists = val;
			},
			close() {

				this.$emit('update:dialogAddWork', false);
				this.imageUrl = [];

				this.resetForm('ruleForm');
				this.selectData=[];
				this.empty = true;

				
				//上传附件清空
				this.resetAttach();
			},
			selectDataMthod(data){
		        this.selectData = data;
		        // console.log("参与人列表",this.selectData);
		    },
		    // 获取课程下主讲人
		    getMainProfessor() {
		    	let data = {
		    		courseId: this.courseId
		    	}

		    	work.getMainProfessor(data, (res => {
		    		// console.log("getMainProfessor",res)
		    		if(res.status == 200) {
		    			this.expertsName = res.data.name;
		    			this.expertsId = res.data.id;
		    		} else {
		    			this.$message.error(res.message);
		    		}
		    	}));
		    },
		    selAvatar (event) {
		        if(this.num == 9){
		          this.$message.error("最多只能添加9张图片");
		          return;
		        }
		        let _this = this;
		        let extend = event.target.value.substr(event.target.value.lastIndexOf(".") + 1).toLowerCase();
		        if (extend == "jpg" || extend == "png" || extend == "gif" || extend == "jpeg") {
			        let objUrl = event.target.files[0];
			        _this.ruleForm.photo.push(objUrl);
			        let windowURL = window.URL || window.webkitURL;
			        let imageUrl = windowURL.createObjectURL(objUrl);
					_this.imageUrl.push(imageUrl);
					this.num++;
		        }

		        
		        // console.log("imageurl ", document.getElementById("fileName").value);
		    },

			submitForm(formName) {
				this.$refs[formName].validate((valid) => {
					if (valid) {
						this.createWork();
					} else {
						console.log('error submit!!');
						return false;
					}
				});
			},
			resetForm(formName) {
		        this.$refs[formName].resetFields();
		    },
			createWork() {

				let _this = this;
				// 是否制定参与人
				if(this.ruleForm.members == 'all') {
					this.specifyStudent = false;
				} else {
					this.specifyStudent = true;
				}


				// 所选学员id 数组
				this.selectData.forEach((item,index) => {
		          	this.ids.push(item["id"]);
		        });


				let beginTime = this.ruleForm.beginTime ? dateFormatStr(this.ruleForm.beginTime) : "";
				let	endTime = this.ruleForm.endTime ? dateFormatStr(this.ruleForm.endTime) : "";

				let data = {
					title: this.ruleForm.title,
					content: this.ruleForm.content,
					courseId: this.courseId,
					beginTime: beginTime,
					endTime: endTime,
					specifyStudent: this.specifyStudent,
					ids: this.ids,
					openMode: this.ruleForm.openmode,
					'errCallBack' : this.storeNotEnough,

				}


				// console.log("创建作业",data, this.ruleForm.photo);
				this.loading = true;

				work.createWork(data, (res => {
					if(res.status == 200) {
						// console.log(res)
						this.close();



						this.loading = false;
						// 初始值清空
			            /*选择成员数值清空*/

			            this.selectData = [];
			            this.ruleForm.title="";
			            this.ruleForm.content="";
			            this.ruleForm.beginTime="";
			            this.ruleForm.endTime="";
			            this.ruleForm.members = 'all';
			            this.ruleForm.specifyStudent = false;

			            this.ruleForm.photo=[];
			            this.imageUrl = [];
			            document.getElementById("fileName").value = "";
						this.ids = [];
						this.ruleForm.openmode = "TIMING";

						//上传附件清空
						this.resetAttach();

						 this.num = 0;
						 //发布成功路由跳转到作业页面
						 // clazzId:clazzId, courseId:courseId, activityId: task.id, crumb:'task'
						 this.clazzId=res.data.clazzId
						 this.courseId2=res.data.courseId
						 this.activityId=res.data.id
						 if(this.crumb=="项目任务"){
							this.crumb='task'
						 }else{
							this.crumb='det' 
						 }
						 this.$emit("createmywork")
						//  this.$router.push({ path: '/activity/homework/result', query: {clazzId:this.clazzId,courseId:this.courseId2, activityId: this.activityId,crumb:this.crumb,open:true}})
					} else {
						if(res.status == 423){
							this.overLimit = true;
						}else{
							this.$message.error(res.message);
						}
						this.loading = false;

						//上传附件清空
						// this.resetAttach();
					}
				}), this.ruleForm.photo,this.fileLists)

			},
			membersChange() {
				if(this.ruleForm.members == "all"){
		          	this.specifyStudent = false;

		        }else{
		          	this.specifyStudent = true;
				}
				if(this.ruleForm.members == "groupSel"){
					this.selectGroups=true;
				}else{
					this.selectGroups=false;
				}
			},
			deletePic(index,event){
				this.num--;
				if(this.num == 0){
					this.num = 0;
				}
				this.imageUrl.splice(index,1);
				this.ruleForm.photo.splice(index,1);
				document.getElementById("fileName").value = "";
			},
			//分组列表
			getGroupList(){
				work.groupList({clazzId:this.$route.query.clazzId},(res)=>{
					if(res.status==200&&res.data){
						this.canSelectGroup = true;
					}
				})
			},
			//小组成员列表
			getgroupUserList(group){
				let keyArr = [];
				for(let item in group){
					let key = "p"+item;
					key = new Promise((resolve,reject)=>{
						work.groupUserList({clazzId:this.$route.query.clazzId,id:group[item].id},(res)=>{
							console.log(res);
							if(res.status==200&&!!res.data){
								resolve(res.data);
							}
							
						})
					})
					keyArr.push(key);
				}
				Promise.all(keyArr).then(res=>{
					let resultArr = [];
					for(const item of res){
						resultArr = resultArr.concat(item);
					}
					this.selectData = resultArr;
				})
			},
			// 存储空间不足
			storeNotEnough(error){
				console.log(error.response);
				this.loading = false;
				if(error.response.status == 423){
					this.overLimit = true;
				};
			},
		},
		components:{
	      	studentSelect,

			openUpload,
			overLimitPop,
	    }
	}
</script>
