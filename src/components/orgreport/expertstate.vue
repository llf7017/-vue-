<template>
	<div class="w-expertstate">

		<section class="handle clearfix">
			<span>
				<Goback passtitle="专家授课情况" ></Goback>
				<span class="tip2">（注：数据统计截止到昨日）</span>
			</span>
			<div class="right">
				<el-select v-model="keywordType" >
				    <el-option
				      	v-for="item in options"
				      	:key="item.value"
				      	:label="item.label"
				      	:value="item.value">
				    </el-option>
				</el-select>
				<div class="search">
					<input type="text" placeholder="请输入内容"  v-model="keyword" @keyup.13="searchData"/><i class="el-icon-search" @click.stop="searchData"></i>
				</div>
				<button class="btn-green" type="button"  @click="resExport">
					<i class="icon-export-white"></i>
					导出
				</button>
			</div>
		</section>

		<section class="wcont round-layout" id="w-table">
			<el-table class="list"
				:data = "items"
				style="text-align:center"
                :stripe="true"
                :header-cell-style="{'text-align':'center'}"
                :empty-text="loading_font"
                tooltip-effect="dark"
                @cell-click="cellclick"
				>
				<el-table-column
					prop=""
					label="专家姓名"
					width="">
					<template slot-scope="scope">
						<el-tooltip class="item" effect="dark" :content="scope.row.professorName" placement="bottom-start">
							<span>{{scope.row.professorName}}</span>
					    </el-tooltip>
						
					</template>
				</el-table-column>
				<el-table-column
					prop=""
					label="课程"
					width="180">
					<template slot-scope="scope">
						<el-tooltip class="item" effect="dark" :content="scope.row.courseName" placement="bottom-start">
							<span>{{scope.row.courseName}}</span>
					    </el-tooltip>
						
					</template>
				</el-table-column>
				<el-table-column
					prop="courseType"
					label="课程类型"
					width="100">
				</el-table-column>
				<el-table-column
					prop=""
					label="所属班级"
					width="180">
					<template slot-scope="scope">
						<el-tooltip class="item" effect="dark" :content="scope.row.clazzName" placement="bottom-start">
							<span class="blue">{{scope.row.clazzName}}</span>
					    </el-tooltip>
						
					</template>
				</el-table-column>
				<el-table-column
					prop="courseStatistics"
					label="课程满意度"
					width="110">
				</el-table-column>
				<el-table-column
					prop="beginTime"
					label="开始时间"
					width="140">
				</el-table-column>
				<el-table-column
					prop="endTime"
					label="结束时间"
					width="140">
				</el-table-column>

				<el-table-column
					prop=""
				    label="上课地点"
				    width="100">
				    <template slot-scope="scope">
						<el-tooltip class="item" effect="dark" :content="scope.row.location" placement="bottom-start">
							<span>{{scope.row.location}}</span>
					    </el-tooltip>
					</template>
			    </el-table-column>
			</el-table>
			<p class="page-sum">共{{totalElements}}条 每页10条</p>
			<!-- 分页 -->
			<div class="pagination">
		      	<el-pagination
		        	@current-change ="handleCurrentChange"
		        	:current-page="cur_page"
		        	layout="prev, pager, next"
		        	:page-size ="10"
		        	:total="totalElements">
		      	</el-pagination>
		    </div>
		</section>

	</div>
</template>
<script>
	import orgReport from 'model/orgreport/orgreport'

	import { getCurRole } from '@/filters/userVerify'
	import API from '@/global/resource';
	import { getToken } from '@/filters/userVerify'

	import Goback from 'base/goback/goback'

	export default {
		data() {
			return {
				role: getCurRole(),
				items: [],
				cur_page: 1,
		        totalElements: 0,
		        options: [{
		          	value: 'professor',
		          	label: '专家名称'
		        }, {
		          	value: 'course',
		          	label: '课程名称'
		        }, {
		          	value: 'clazz',
		          	label: '班级名称'
		        }],
		        keywordType: 'clazz',
		        keyword: "",
		        loading: true,
		        loading_font:this.$globalParam.loadText,
			}
		},
		computed: {
			orgId() {
				console.log("orgId =======",this.$route.query.orgId);
				return this.$route.query.orgId;
			}
		},
		mounted() {
			console.log("总结报告 role ",this.role );
			if(this.role == 'PROJECTADMIN') {
				this.getItemData();
			} else {
				this.getOrgData();
			}
		},
		methods: {
			handleCurrentChange (val) {
				this.cur_page = val;
        		if(this.role == 'PROJECTADMIN') {
					this.getItemData();
				} else {
					this.getOrgData();
				}
        		console.log("number", this.cur_page);
			},
			searchData() {
				this.cur_page = 1;
				if(this.role == 'PROJECTADMIN') {
					this.getItemData();
				} else {
					this.getOrgData();
				}
			},
			// 机构-专家授课情况
			getOrgData() {
				let data = {
					keyword: this.keyword,
					keywordType: this.keywordType,
					number: this.cur_page,
					orgId: this.orgId,
					size: 10
				}

				orgReport.RepOrgProfessor(data, (res => {
					console.log('机构-专家授课情况', res);
					if(res.status == 200) {
						this.items = res.data.content;
						this.totalElements = res.data.totalElements;

						this.loading_font=this.$globalParam.dataEmpty;
						this.loading = false;
					} else {
						this.$message.error(res.message);
						this.loading = false;
					}
				}));
			},

			// 项目-专家授课情况
			getItemData() {
				let data = {
					keyword: this.keyword,
					keywordType: this.keywordType,
					number: this.cur_page,
					orgId: this.orgId,
					size: 10
				}

				orgReport.RepItemProfessor(data, (res => {
					console.log('项目-专家授课情况', res);
					if(res.status == 200) {
						this.items = res.data.content;
						this.totalElements = res.data.totalElements;

						this.loading_font=this.$globalParam.dataEmpty;
						this.loading = false;
					} else {
						this.$message.error(res.message);
						this.loading = false;
					}
				}));
			},
			//进入班级
	       	cellclick(row, column, cell, event){
	            if(column.label=='所属班级'){
	                window.open("#/clazz/index?clazzId="+row.clazzId)
	            }
	        },

	        resExport() {
				if(!this.$store.getters.getOrgVipInfo.vip){
					this.$store.state.vipFuncPopFalg = true;
					return;
				}
				if(this.role == 'PROJECTADMIN') {
					window.open(API.urls.RepItemExpProfessor+"?orgId="+this.orgId+"&token="+getToken());
				} else {
					window.open(API.urls.RepOrgExpProfessor+"?orgId="+this.orgId+"&token="+getToken());
				}
				
			}
		},

		components: {
			Goback
		}
	}
</script>
<style scoped lang="less">
	@import "../../assets/less/icon.less";
	@import "../../assets/less/btn-search.less";
	@import "../../assets/reset-element-ui/user-table.css";
	.search {
		background: #fff;
		float: none;
		display: inline-block;
		margin-right: 15px;
	}
	.el-select {
		width: 120px;
		margin-right: 5px;
	}

</style>