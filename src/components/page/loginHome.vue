<template>
  <div class="page-box">
    <div class="page-title"><span><i class='el-icon-s-flag'></i></span>首页面板</div>
    <el-row class="outbox" :gutter="40">
      <el-col :span="6">
        <div class="con bg-info">
          <div class="num-box"><span class="num">243</span>个</div>
          <div class="sts">总任务</div>
          <i class="el-icon-aim"></i>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="con bg-primary">
          <div class="num-box"><span class="num">38</span>个</div>
          <div class="sts">进行中</div>
          <i class="el-icon-stopwatch"></i>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="con bg-success">
          <div class="num-box"><span class="num">200</span>个</div>
          <div class="sts">已完成</div>
          <i class="el-icon-circle-check"></i>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="con bg-danger">
          <div class="num-box"><span class="num">5</span>个</div>
          <div class="sts">异常</div>
          <i class="el-icon-warning-outline"></i>
        </div>
      </el-col>
    </el-row>

    <el-row :gutter="40">
      <el-col :span="8">
        <div class="userinfo bg-fff center">
          <div class="small-title"><i class="el-icon-info"></i>个人信息</div>
          <div class="userimg"><img src="../../assets/image/headImg.jpg"></div>
          <div>刘德华 <span class="level bg-success">高级VIP7</span></div>
          <div>账户余额：￥ <span class="money">28888</span></div>
          <div>
            <el-button type="primary" @click='recharge'>充值</el-button>
            <el-button type="danger" class="ml30" @click="cashMoney">提现</el-button>
          </div>
        </div>
      </el-col>
      <el-col :span="16">
        <div class="charts-xf-box bg-fff">
          <div id="account" style="height:400px;"></div>
        </div>
      </el-col>
    </el-row>

    <el-row :gutter="40">
    <el-col :span="12">
      <div class="product-list bg-fff">
        <div class="small-title"><i class="el-icon-info"></i>亚马逊产品</div>
        <el-table :data="tableData1" style="width: 100%" :header-cell-style="{background:'#fff'}">
          <el-table-column prop="name" label="产品" align="center"></el-table-column>
          <el-table-column prop="price" label="价格" align="center"></el-table-column>
          <el-table-column prop="remark" label="简介" align="center" width="400"></el-table-column>
          <el-table-column prop="" label="购买" align="center">
            <template slot-scope="scope">
              <el-button size="small" type="success" v-show="scope.row.name=='亚马逊FBA任务'" @click="fbaTask" style="margin-left: 8px;">购买</el-button>
              <el-button size="small" type="primary" v-show="scope.row.name=='亚马逊加购任务'" @click="buyCarTask">购买</el-button>
              <el-button size="small" type="warning" v-show="scope.row.name=='亚马逊心愿任务'" @click="wishTask">购买</el-button>
              <el-button size="small" type="success" v-show="scope.row.name=='亚马逊点赞任务'" @click="likesTask">购买</el-button>
              <el-button size="small" type="primary" v-show="scope.row.name=='亚马逊QA任务'" @click="QaTask">购买</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </el-col>
    <el-col :span="12">
    <template>
      <div class="product-list bg-fff">
        <div class="small-title"><i class="el-icon-info"></i>速卖通产品</div>
        <el-table :data="tableData2" style="width: 100%" :header-cell-style="{background:'#fff'}">
          <el-table-column prop="name" label="产品" align="center"></el-table-column>
          <el-table-column prop="price" label="价格" align="center"></el-table-column>
          <el-table-column prop="remark" label="简介" align="center" width="400"></el-table-column>
          <el-table-column prop="" label="购买" align="center">
            <template slot-scope="scope">
              <el-button size="small" type="success" v-show="scope.row.name=='速卖通FBA任务'" @click="fbaTask" style="margin-left: 8px;">购买</el-button>
              <el-button size="small" type="primary" v-show="scope.row.name=='速卖通加购任务'" @click="buyCarTask">购买</el-button>
              <el-button size="small" type="warning" v-show="scope.row.name=='速卖通心愿任务'" @click="wishTask">购买</el-button>
              <el-button size="small" type="success" v-show="scope.row.name=='速卖通点赞任务'" @click="likesTask">购买</el-button>
              <el-button size="small" type="primary" v-show="scope.row.name=='速卖通QA任务'" @click="QaTask">购买</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </template>
    </el-col>
    </el-row>
    <!--提示-->
    <div class="errTip">
      <el-dialog title="提示" :visible.sync="errorModel" width="30%" center>
        <span>您有<span class="fz16 redRequired">{{errorNum}}</span>条异常信息，请前往处理！</span>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="toErrorList">确 定</el-button>
        </span>
      </el-dialog>
    </div>
    <!--充值-->
    <el-dialog title='充值' :visible.sync='RechargeModal' :close-on-click-modal='false'>
      <el-form :model='rechargeForm' ref='rechargeForm' class="form-item" :rules='rechargeRules' :inline="true"
        label-width="80px">
        <el-row>
          <el-col>
            <el-form-item label='充值金额'>
              <el-radio-group v-model="rechargeForm.money">
                <el-radio-button label="100元"></el-radio-button>
                <el-radio-button label="300元"></el-radio-button>
                <el-radio-button label="500元"></el-radio-button>
                <el-radio-button label="800元"></el-radio-button>
                <el-radio-button label="1000元"></el-radio-button>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col>
            <el-form-item label='自定义' prop='moneys'>
              <el-input v-model='rechargeForm.moneys' class='disInline'></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item label='付款方式'>
          <el-radio-group v-model="rechargeForm.payType">
            <el-radio :label="1">支付宝</el-radio>
            <el-radio :label="2">微信</el-radio>
            <el-radio :label="3">银行卡</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <div slot='footer' class="dialog-footer">
        <el-button type='primary'>确认</el-button>
        <el-button @click='RechargeModal=false'>取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  // import echarts from 'echarts'
  var echarts = require('echarts/lib/echarts')
  // 引入柱状图组件
  require('echarts/lib/chart/Line')
  // 引入提示框和title组件
  require('echarts/lib/component/tooltip')
  require('echarts/lib/component/title')
  require("echarts/lib/component/legend")
  import bus from '../common/bus'
  import vali from '../common/validate'
  export default {
    name: 'loginHome',
    data() {
      return {
        item: '1',
        RechargeModal: false, //充值
        errorModel: false,
        collapse: false,
        errorNum: '0',
        name: sessionStorage.getItem('userName'),
        times: sessionStorage.getItem('times'),
        rechargeForm: {
          money: '',
          moneys: '',
          payType: ''
        },
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
        tableData1: [{
          name: '亚马逊FBA任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '亚马逊加购任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '亚马逊心愿任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '亚马逊点赞任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '亚马逊QA任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }],
        tableData2: [{
          name: '速卖通FBA任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '速卖通加购任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '速卖通心愿任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '速卖通点赞任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }, {
          name: '速卖通QA任务',
          price: '100起',
          remark: '亚马逊FBA订单亚马逊FBA订单亚马逊亚马逊亚马逊'
        }]
      }
    },
    created() {
      bus.$on('collapse', msg => {
        this.collapse = msg
      })
      // this.getSrc()
      this.isError()
    },
    computed: {
      role() {
        return this.name === 'admin' ? '超级管理员' : '普通用户'
      }
    },
    mounted() {
      this.account()
    },
    methods: {
      //充值
      recharge() {
        let _this = this
        _this.RechargeModal = true
      },
      isError() {
        let _this = this
        let err = _this.errorNum
        if (err > 0) {
          _this.errorModel = true
        }
      },
      // 前往异常订单
      toErrorList() {
        this.$router.push({
          name: 'taskManage',
          params: {
            active: 7
          }
        })
      },
      // 更多任务
      moreTask() {
        let _this = this
        let status = true
        _this.$router.push({
          name: "taskManage",
          params: {
            taskTypeModel: true
          }
        })
      },
      // FBA任务
      fbaTask() {
        let _this = this
        _this.$router.push('/FbaTask')
      },
      // 心愿任务
      wishTask() {
        let _this = this
        _this.$router.push({
          name: 'wishTask'
        })
      },
      //购物车任务
      buyCarTask() {
        let _this = this
        _this.$router.push('/buyCarTask')
      },
      //点赞任务
      likesTask() {
        let _this = this
        _this.$router.push('/likesTask')
      },
      //QA任务
      QaTask() {
        let _this = this
        _this.$router.push('/QaTask')
      },
      // 提现
      cashMoney() {
        this.$router.push('/CashWithdrawal')
      },
      getSrc() {
        let _this = this
        if (_this.name === 'admin') {
          _this.hashBd = true
        } else {
          _this.hashBd = false
        }
      },
      account() {
        var dom = document.getElementById('account')
        var myChart = echarts.init(dom)
        var option = {
          title: {
            text: '近12个月消费趋势图'
          },
          tooltip: {
            trigger: 'axis'
          },
          legend: {
            data: ['账平','账不平']
          },
          grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          toolbox: {
            feature: {
              saveAsImage: {}
            }
          },
          xAxis: {
            type: 'category',
            boundaryGap: false,
            data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
          },
          yAxis: {
            type: 'value'
          },
          color: ['#9a55ff','#fe7096'],
          series: [{
              smooth: true,
              name: '账平',
              type: 'line',
              stack: '总计',
              data: [20, 52, 101, 34, 90, 130, 210, 300, 100, 150, 11, 180]
            },
            {
              smooth: true,
              name: '账不平',
              type: 'line',
              stack: '总计',
              data: [30, 62, 88, 55, 75, 180, 150, 200, 90, 120, 100, 80]
            }
          ]
        }
        myChart.setOption(option)
      }
    }
  }
</script>

<style scoped>
  .el-row {
    margin-bottom: 20px;
  }

  .todo-item {
    font-size: 14px;
  }

  .todo-item-del {
    text-decoration: line-through;
    color: #999;
  }

  .isRed {
    display: inline-block;
    height: 100px;
    width: 100%;
    color: red;
  }
</style>
