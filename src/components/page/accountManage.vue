<template>
  <div class="fbox">
    <div class="page-title mb30"><span><i class='el-icon-s-marketing'></i></span>收支明细</div>
    <div class="searchBox">
      <el-form ref="form" :model="form" label-width="80px" :inline="true" class="form-item">
        <el-row>
          <el-col :span="7">
            <el-form-item label="交易日期" class="form-item">
              <el-date-picker type="date" placeholder="选择开始时间" v-model="form.startTime"
                value-format="yyyy-MM-dd" :picker-options="pickerStartDate" style="width: 150px;"></el-date-picker>
              <el-date-picker type="date" placeholder="选择结束时间" v-model="form.endTime" value-format="yyyy-MM-dd"
                :picker-options="pickerEndDate" style="width: 150px;"></el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="业务编号" class="form-item">
              <el-input v-model="form.name" placeholder="请输入业务编号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item>
              <el-button type="primary" class="ml30" size="medium">搜索</el-button>
              <el-button size="medium">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="Numbers" label="任务编码"></el-table-column>
      <el-table-column prop="Numbers" label="业务编号"></el-table-column>
      <el-table-column prop="ProductPrice" label="交易金额"></el-table-column>
      <el-table-column prop="ProductPrice" label="账户余额"></el-table-column>
      <el-table-column prop="ServiceType" label="交易类型"></el-table-column>
      <el-table-column prop="OrderTime" label="交易时间"></el-table-column>
      <el-table-column prop="remark" label="备注"></el-table-column>
    </el-table>
    <div class="mt20">
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage4"
        :page-sizes="[10, 20, 30, 40]" :page-size="10" layout="total, sizes, prev, pager, next, jumper" :total="1">
      </el-pagination>
    </div>
  </div>
</template>

<script>
  import vali from '../common/validate'
  export default {
    name: 'accountManage',
    data() {
      return {
        currentPage4: 4,
        RechargeModal: false,
        tableData: [],
        form: {
          name: '',
          resource: '',
          startTime: '',
          endTime: ''
        },
        rechargeForm: {
          money: '',
          moneys: '',
          payType: ''
        },
        tableData: [{
          Numbers: '456465',
          ProductPrice: '3000',
          ServiceType: '平台下单',
          OrderTime: '2019-09-09',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          Numbers: '456465',
          ProductPrice: '3000',
          ServiceType: '平台下单',
          OrderTime: '2019-09-09',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          Numbers: '456465',
          ProductPrice: '3000',
          ServiceType: '平台下单',
          OrderTime: '2019-09-09',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          Numbers: '456465',
          ProductPrice: '3000',
          ServiceType: '平台下单',
          OrderTime: '2019-09-09',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }],
        rechargeRules: {
          moneys: [{
              validator: vali.proPrice,
              trigger: 'change'
            },
            {
              required: false,
              trigger: 'change'
            }
          ]
        },
        pickerEndDate: this.pickerOptionsEnd(),
        pickerStartDate: this.searchStartDate(),
        name: 'fd'
      }
    },
    methods: {
      // 搜索时间
      searchStartDate() {
        return {
          disabledDate: time => {
            let endDateVal = this.form.endTime
            if (endDateVal) {
              return time.getTime() > new Date(endDateVal).getTime()
            }
          }
        }
      },
      // 搜索下单结束时间
      pickerOptionsEnd() {
        return {
          disabledDate: time => {
            let beginDateVal = this.form.startTime
            if (beginDateVal) {
              return (
                time.getTime() <
                new Date(beginDateVal).getTime() - 1 * 24 * 60 * 60 * 1000
              )
            }
          }
        }
      },
      // 提现
      cashPage() {
        this.$router.push('/CashWithdrawal')
      },
      handleSizeChange (val) {
        console.log(`每页 ${val} 条`)
      },
      handleCurrentChange (val) {
        console.log(`当前页: ${val}`)
      }
    }
  }
</script>

<style scoped>
  /*@import '../../../static/css/mystyle.css';*/

  .line {
    text-align: center;
    margin-right: 10px;
  }
</style>
