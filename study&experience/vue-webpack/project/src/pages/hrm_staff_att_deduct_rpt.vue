<template>

  <div class="info" style="width: 1061px">
    <div class="infoPlace">
      您所在的位置是：
      <span>人事管理 / 职工病事假扣费汇总</span>
    </div>
    <div class="infoTitle clearfix">
      <div class="roster fl">
        <span>|</span>&nbsp;职工病事假扣费汇总</div>
      <ul class="fr clearfix">
        <li class="fl " id="vacation">
          <e class="calendar"></e>
          <select style="border: none">
            <option value="2017">2017年</option>
            <option value="2016">2016年</option>
            <option value="2015">2015年</option>
            <option value="2014">2014年</option>
          </select>
          <div class="cover"></div>
          <div class="line"></div>
          <select style="border: none">
            <option value="2017">1月</option>
            <option value="2016">2月</option>
            <option value="2015" selected="selected">3月</option>
            <option value="2014">4月</option>
            <option value="2017">5月</option>
            <option value="2016">6月</option>
            <option value="2015">7月</option>
            <option value="2014">8月</option>
            <option value="2017">9月</option>
            <option value="2016">10月</option>
            <option value="2015">11月</option>
            <option value="2014">12月</option>
          </select>
          <div class="cover covertwo"></div>
          <div class="triangle"></div>
          <div class=" up"></div>
        </li>
        <li class="fl">
          <a href="javascript:void(0)" @click="cmdDetailExpExcel">
            <e class="export"></e>
            导出</a>
        </li>
        <li class="fl">
          <a href="javascript:void(0)">
            <e class="print"></e>
            打印</a>
        </li>
      </ul>
    </div>
    <div class="roster-message" style="min-height: 660px;">
      <table border="0" cellspacing="1" cellpadding="10" align="center" 
      	id="dataGridTableJson" class="table tablelist">
        <thead>
          <tr class="r-t-head">
            <th class="first-td">编号</th>
            <th>姓名</th>
            <th>事由</th>
            <th>休假天数</th>
            <th>扣款金额</th>
            <th>科室</th>
            <th>备注</th>
          </tr>
        </thead>

        <tbody id="tbody">
          <tr v-for="(item,index) in tableResultList.rows">
            <td>{{item.CODE}}</td>
            <td>{{item.STAFF_ID_NAME}}</td>
            <td>{{item.REASON_TYPE_ID_NAME}}</td>
            <td>{{item.DAYS}}</td>
            <td>{{item.MONEY}}</td>
            <td>{{item.DEPT_ID_NAME}}</td>
            <td>{{item.COMMENTS}}</td>
          </tr>
        </tbody>
      </table>

    </div>
    <div class="infoBOttom clearfix">
         <div class="block">
              <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[10, 20, 50, 100]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="tableResultList.total">
              </el-pagination>
              <!-- <el-button-group>
                  <el-button type="primary" icon="arrow-left">上一页</el-button>
                  <el-button type="primary">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
              </el-button-group> -->
        </div>    
        <div class="showtotal" @click="cmdShowTotal">显示全部
        </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pageSize: 10,
      currentPage: 1,
      tableResultList: [],
    }
  },
  methods: {
    cmdShowTotal(){
      this.currentPage = 1;
      this.pageSize= -1;
      this.comGetDatas();
    },
    comGetDatas() {
      var param = this.queryParam(this.currentPage, this.pageSize, "", "", "", "201709");
      var url = this.common.baseNodeUrl+"hrm/rpt/Srv_HRM_Staff_AttendDeductRpt.ashx" + param  
      this.$http.get(url)					  		 
					.then(function(res) {
						var dataJson = res.body;
						console.log(dataJson)
						if(dataJson.Succeed != 1) {
							if(dataJson.Message!=null)
							{
								alert(dataJson.Message);
							}
							console.log("职工病事假扣费汇总数据失败");
							return false;
						}
						this.tableResultList = dataJson.Message;
					}).catch(function (res) {
	        			  console.log(res+"职工病事假扣费汇总数据异常")
					});		
    },
    // 导出
    cmdDetailExpExcel(){
        var url = this.common.baseNodeUrl+"hrm/rpt/Srv_HRM_Staff_AttendDeductExpExcel.ashx";
         this.$http.get(url)             
					.then(function(res) {
						var dataJson = res.body;
						if(dataJson.Succeed != 1) {
							if(dataJson.Message!=null)
							{
								alert(dataJson.Message);
							}
							console.log("职工病事假扣费汇总导出数据失败");
							return false;
						}
						window.location.href = dataJson.Message;
					}).catch(function (res) {
	        			  console.log(res+"职工病事假扣费汇总导出数据异常")
					});		
    },
    handleSizeChange(val) {
      this.pageSize = val;
      // this.currentPage = 1;
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.comGetDatas();
    },
    queryParam(pageNumber, pageSize, sortName, sortOrder, condition, financeCycle) {
      var param = "?";
      if (pageSize != undefined) {
        param += 'pageSize=' + pageSize;
      } else {
        param += 'pageSize=10';
      }

      if (pageNumber != undefined) {
        param += '&pageNumber=' + pageNumber;
      } else {
        param += '&pageNumber=1';
      }

      if (sortName != undefined) {
        param += '&sortName=' + sortName;
      } else {
        param += '&sortName=ID';
      }

      if (sortOrder != undefined) {
        param += '&sortOrder=' + sortOrder;
      } else {
        param += '&sortOrder=desc';
      }

      if (condition != undefined) {
        param += '&condition=' + condition;
      } else {
        param += '&condition=';
      }

      if (financeCycle != undefined) {
        param += 'financeCycle=' + financeCycle;
      } else {
        param += 'financeCycle=201709';
      }

      return param
    }
  },
  created() {
    this.comGetDatas();
  },
}
</script>
<style scoped>
#vacation {
  display: inline-block;
  background: #fff;
  border-radius: 100px;
  line-height: 28px;
  text-decoration: none;
  font-family: PingFangSC-Regular;
  font-size: 12px;
  color: #5d89b3;
  text-align: center;
  margin-left: 20px;
  position: relative;
  border: 1px solid #5d89b3;
  width: 158px;
  height: 28px;
  margin-top: 23px;
  padding-left: 10px;
}

.calendar {
  display: inline-block;
  width: 13px;
  height: 13px;
  background: url("images/icon_date.png");
  position: absolute;
  top: 7px;
  left: 6px;
}

select {
  position: relative;
  outline: none;
}

.cover {
  width: 20px;
  height: 20px;
  position: absolute;
  top: 6px;
  left: 80px;
  background-color: #fff;
}

.line {
  height: 1px;
  width: 9px;
  background-color: #5d89b3;
  position: absolute;
  top: 13px;
  left: 83px;
}

.covertwo {
  top: 6px;
  left: 132px;
}

.triangle {
  position: absolute;
  top: 16px;
  right: 26px;
  width: 0;
  height: 0;
  border-top: 4px solid #5d89b3;
  border-left: 3px solid transparent;
  border-bottom: 4px solid transparent;
  border-right: 3px solid transparent
}

.up {
  position: absolute;
  top: 5px;
  right: 26px;
  width: 0;
  height: 0;
  border-left: 3px solid transparent;
  border-right: 3px solid transparent;
  border-bottom: 4px solid #5d89b3;
  border-top: 4px solid transparent;
}

.export {
  background: url("images/icon_export_01.png");
}

.print {
  background: url("images/icon_print_01.png");
}

.tablelist{
	border-collapse:separate;
	border-spacing: 0px 10px;
	margin-right:30px;
}
.infoBOttom{
  position: relative;
}
</style>
