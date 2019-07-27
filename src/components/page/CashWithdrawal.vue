<template>
  <div class="fbox">
    <div class="mb20 tabsTxt">
      <span>首页</span>
      <span class="fg">/</span>
      <span>提现</span>
    </div>
    <div class="balanceBox mb20">
      <div class="ml30 mr30">账户余额</div>
      <div>￥<span class="money">{{hasMoney}}</span></div>
      <div class="ml30">
        <div v-if="hasMoney<=0">
          <el-tooltip placement="top" effect="light" popper-class="tipBox">
            <div slot="content" class="fz14">不可提现原因<br /><span class="col9 reason">可提现金额不足</span></div>
            <el-button type="danger" size="medium">立即提现</el-button>
          </el-tooltip>
        </div>
        <div v-else>
          <el-button type="primary" size="medium" @click="takeMoney">立即提现</el-button>
        </div>
      </div>
    </div>
    <div class="searchBox">
      <el-form :model="cashForm" :inline="true" label-width="80px" class="form-item">
        <el-row>
          <el-col :span="6">
            <el-form-item label="提现状态">
              <el-select v-model="cashForm.status" placeholder="请选择">
                <el-option v-for="item in cashForm.options" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>

            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="交易时间">
              <el-date-picker class="mb10" v-model="cashForm.startTime" type="date" placeholder="选择开始时间" value-format="yyyy-MM-dd"
                :picker-options="pickerStartDate" style="width: 150px;"></el-date-picker>
              <el-date-picker v-model="cashForm.endTime" type="date" placeholder="选择结束时间" value-format="yyyy-MM-dd"
                :picker-options="pickerEndDate" style="width: 150px;"></el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="业务编码" class="labelNum">
              <el-input v-model="cashForm.numNo" style="width: 220px" placeholder="请输入业务编码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="5">
            <el-form-item>
              <el-button type="primary" size="medium">搜索</el-button>
              <el-button size="medium" @click="resetForm">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <el-table :data="allCashData" border style="width: 100%">
      <el-table-column prop="Numbers" label="业务编码" align="center"></el-table-column>
      <el-table-column prop="Picture" label="提现金额" align="center"></el-table-column>
      <el-table-column prop="CountryId" label="提现状态" align="center"></el-table-column>
      <el-table-column prop="ProductByASIN" label="提现时间" align="center"></el-table-column>
      <el-table-column prop="ProductPrice" label="开户银行" align="center"></el-table-column>
      <el-table-column prop="ServiceType" label="开户名" align="center"></el-table-column>
      <el-table-column prop="OrderNote" label="开户银行账号" align="center"></el-table-column>
      <el-table-column label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button size="small" type="primary">查看</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!--提现-->
    <el-dialog title='提现' :visible.sync='takeMoneyModal' :close-on-click-modal='false'>
      <el-form :model='takeMoneyForm' ref='takeMoneyForm' class="form-item"
        label-width="80px">
        <el-form-item label='提现金额'>
          <el-input v-model='takeMoneyForm.money'></el-input>
        </el-form-item>
        <el-form-item label='提现到'>
          <el-radio-group v-model="takeMoneyForm.type">
            <el-radio label="1">支付宝</el-radio>
            <el-radio label="2">微信</el-radio>
            <el-radio label="3">银行卡</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label='账户名'>
          <el-input v-model='takeMoneyForm.name'></el-input>
        </el-form-item>
        <el-form-item label='账  号'>
          <el-input v-model='takeMoneyForm.no'></el-input>
        </el-form-item>
      </el-form>
      <div slot='footer' class="dialog-footer">
        <el-button type='primary'>确认</el-button>
        <el-button @click='takeMoneyModal=false'>取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'CashWithdrawal',
    data() {
      return {
        takeMoneyModal:false,
        hasMoney: '28888',
        pickerEndDate: this.pickerOptionsEnd(),
        pickerStartDate: this.searchStartDate(),
        allCashData: [],
        cashForm: {
          status: '1',
          startTime: '',
          endTime: '',
          numNo: '',
          options: [{
            value: '1',
            label: '全部'
          }, {
            value: '2',
            label: '已完成'
          }, {
            value: '3',
            label: '已取消'
          }],
        },
        takeMoneyForm:{
          money:'',
          type:'1',
          name:'',
          no:''
        }
      }
    },
    methods: {
      // 重置
      resetForm() {
        let _this = this
        _this.cashForm = {
          status: '全部',
          startTime: '',
          endTime: '',
          numNo: ''
        }
      },
      // 搜索开始时间
      searchStartDate() {
        return {
          disabledDate: time => {
            let endDateVal = this.cashForm.endTime
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
            let beginDateVal = this.cashForm.startTime
            if (beginDateVal) {
              return (
                time.getTime() <
                new Date(beginDateVal).getTime() - 1 * 24 * 60 * 60 * 1000
              )
            }
          }
        }
      },
      takeMoney(){
        let _this = this
        _this.takeMoneyModal = true
      }
    }
  }
</script>

<style scoped>
  @import '../../assets/css/mystyle.css';
</style>
