<template>
	<div class="wtrial">
		<section class="handle clearfix">
			<Goback passtitle="商务联系表" ></Goback>
			<p class="right">
				<button class="btn-green" type="button"  @click="exportList">
					<i class="icon-export-white"></i>
					导出
				</button>
			</p>
		</section>

		<section id="w-table" class="round-layout">
			<!-- <h2 class="title">申请试用名单</h2> -->
			<div class="box">
				<el-table class=""
					:data = "tableData"
					stripe
					>
					<el-table-column
						prop="userName"
						label="姓名"
						width="180">
					</el-table-column>
					<el-table-column
						prop="telephone"
						label="手机号"
						width="300"
						>
					</el-table-column>
					<el-table-column
						prop="createDate"
						label="申请时间">
					</el-table-column>					
				</el-table>
			
			
				<p class="page-sum">共{{totalElements}}条 每页20条</p>
				<!-- 分页 -->
				<div class="pagination">
			      	<el-pagination
			        	@current-change ="handleCurrentChange"
			        	layout="prev, pager, next"
			        	:page-size ="20"
			        	:total="totalElements">
			      	</el-pagination>
			    </div>
		    </div>
		</section>
	</div>
</template>
<script>
	import apply from 'model/comtpoc/apply'
	import API from '@/global/resource'
	import { getToken } from '@/filters/userVerify'
	import Goback from 'base/goback/goback'

	export default {
		data() {
			return {
				tableData: [],
				cur_page: 1,
				totalElements: 0
			}
		},
		computed: {

		},
		mounted() {
			this.getList();
		},
		methods: {
			handleCurrentChange (val) {
                console.log(val)
				this.cur_page = val;
        		this.getList();
			},
			getList() {
				let data = {
					size: 20,
					number: this.cur_page
				}
				apply.buschat(data, (res => {
					console.log("商务联系列表", res);
					if(res.status == 200) {
						this.tableData = res.data.content;
						this.totalElements = res.data.totalElements;
					} else {
						this.$message.error(res.message);
					}
				}));
			},
			exportList() {
				window.open(API.urls.exportbuschat+"?token="+getToken());
			}
		},

		components: {
			Goback
		}
	}

</script>
<style scoped lang="less">
	@import "../../../assets/less/icon.less";
	@import "../../../assets/less/btn-search.less";
	@import "../../../assets/reset-element-ui/user-table.css";
	.round-layout {
		box-sizing: border-box;
		padding: 20px 20px 0 20px;
	}
	.box {
		padding: 0 20px;
	}
	.title {
		position: relative;
	    font-size: 20px;
	    line-height: 20px;
	    color: #101010;
	    letter-spacing: 0;
	    margin-bottom: 34px;
	    padding-left: 10px;

	    &:before {
	        content: "";
	        width: 3px;
	        height: 20px;
	        background-color: #35A69D;
	        position: absolute;
	        left: 0;
	    }
	}
	.btn-green::after{
		width: 0;
		height: 0;
	}

</style>