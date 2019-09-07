<template>
  <div class="fbox">
    <div class="mb20">
      <span class="tabsTxt">
        <span>产品</span>
        <span class="fg">/</span>
        <span>FBA任务</span>
      </span>
    </div>
    <el-row>
    <el-col :span="16" :xs="24" :sm="24" :md="24" :lg="16">
      <el-form :model="taskForm" ref="taskForm" :rules="taskRule" class="demo-ruleForm" status-icon>
        <el-row>
          <el-col :span="8" :xs="24">
            <el-form-item label="国家" class="disInline minWid" prop="CountryId">
              <el-select v-model="taskForm.CountryId" placeholder="请选择" class="disInline wid100">
                <el-option v-for="(item,index) in countryData" :key="index" :value="index" :label="item.country"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="产品ASIN" class="disInline minWid" prop="ProductByASIN">
              <el-input v-model="taskForm.ProductByASIN" maxlength="10" show-word-limit placeholder="长度为10的数字和字母组合"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="店铺名称" class="disInline minWid">
              <el-select v-model="taskForm.shopName" placeholder="请选择" class="disInline wid100">
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="产品名称" class="disInline minWid">
              <el-input v-model="taskForm.productName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="产品价格(￥)" class="disInline minWid" prop="productPrice">
              <el-input type="text" v-model="taskForm.productPrice" placeholder="请输入数字">
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="产品评分">
            </el-form-item>
            <p style="position:relative;top:-12px"><el-rate v-model="taskForm.productScore"></el-rate></p>
          </el-col>
          </el-row>

          <el-row>
          <el-col :span="8" :xs="24">
            <el-form-item label="产品图片" class="disInline">
              <img :src="taskForm.productImg" alt="" class="proImg">
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="评论数" class="disInline minWid">
              <el-input type="number" v-model="taskForm.comments" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="品类排名" class="disInline minWid">
              <el-input type="text" v-model="taskForm.categoryRanking" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="13" :xs="24">
            <el-form-item label="产品链接" class="Wid" prop="productLink">
              <el-input type="text" v-model="taskForm.productLink" placeholder="请以http://或者https://开头"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="预估月销量" class="disInline minWid">
              <el-input type="text" v-model="taskForm.ygsalesnum"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <div class="mt30 mb20 t-title">下单信息</div>
        </el-row>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="关键词类型">
              <el-radio-group v-model="taskForm.keywordTypes">
                <el-radio label="产品关键字"></el-radio>
                <el-radio label="CPC关键字"></el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="终端平台">
              <el-radio-group v-model="taskForm.TerminalPlatform">
                <el-radio label="PC"></el-radio>
                <el-radio label="Android"></el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="搜索关键词" class="disInline minWid" prop="searchKeyword">
              <el-input v-model="taskForm.searchKeyword"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="代购数量" class="disInline minWid" prop="buyNum">
              <el-input v-model="taskForm.buyNum" @blur="checkBuyNum" placeholder="请输入正整数"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="留评类型" class="disInline minWid" prop="commentType">
              <el-select v-model="taskForm.commentType" class="disInline wid100" placeholder="请选择" @change="lpType">
                <el-option label="不留评" value="不留评"></el-option>
                <el-option label="10%" value="10%"></el-option>
                <el-option label="70%" value="70%"></el-option>
                <el-option label="100%(真人100%留评,(保3个月以上))" value="100%"></el-option>
                <el-option label="100%(FBA差留评(100%留，保70%))" value="保70"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24">
            <el-form-item label="Feedback数量" class="disInline minWid">
              <el-input type="text" required="false" v-model="taskForm.fbNumber" placeholder="请输入正整数"></el-input>
              <p><span class="tipRed" v-if="parseInt(this.taskForm.fbNumber)>parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.fbNumber)<0">数量必须小于等于订购数量{{taskForm.buyNum}}</span></p>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24" v-show="nolp==true">
            <el-form-item label="评论图片数量" class="disInline minWid">
              <el-input v-model="taskForm.commentImgNum" placeholder="请输入正整数"></el-input>
              <div class="el-form-item__error" v-if="parseInt(this.taskForm.commentImgNum) > parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.commentImgNum)>5">
                数量不能超过订购数量并且最大为5</div>
            </el-form-item>
          </el-col>
          <el-col :span="8" :xs="24" v-show="nolp==true">
            <el-form-item label="评论视频数量" class="disInline minWid">
              <el-input type="text" :required="false" v-model="taskForm.commentVedioNum" placeholder="请输入正整数"></el-input>
              <div class="el-form-item__error" v-if="parseInt(this.taskForm.commentVedioNum) > parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.commentVedioNum)>5">
                数量不能超过订购数量并且最大为5</div>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="是否使用优惠券">
              <el-radio-group v-model="taskForm.coupon">
                <el-radio label="否"></el-radio>
                <el-radio label="是"></el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="是否自发货">
              <el-radio-group v-model="taskForm.coupon">
                <el-radio label="否"></el-radio>
                <el-radio label="是"></el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <div class="con">
          <el-row :gutter="20">
            <el-col :span="6" :xs="12" :md="7" :lg="5" :xl="6">
              <span class="mb20 disInline">任务开始时间</span>
            </el-col>
            <el-col :span="12" :xs="24" :md="9" :lg="14" :xl="10" class="mb10">
              <el-button type="primary" size="small" @click="oneDay" :disabled="btnTask">一天一单</el-button>
              <el-button type="primary" size="small" @click="twoDay" :disabled="btnTask">一天两单</el-button>
              <el-button type="primary" size="small" @click="threeDay" :disabled="btnTask">一天三单</el-button>
            </el-col>
            <el-col :span="4" :xs="8" :md="6" :lg="3" :xl="6" class="mb10">
              <span>数量</span>
            </el-col>
          </el-row>
          <div v-for="(item,index) in taskForm.taskData" :key="index" class="mb10">
            <el-row :gutter="20">
              <el-col :xs="24" :sm="8" :md="7" :xl="7" class="mb10">
                <el-form-item :prop="`taskData[${index}].startTime`" :rules="{required: true, message: '任务时间不能为空', trigger: 'change'}">
                  <el-date-picker style="width: 100%" v-model="item.startTime" type="date" placeholder="选择日期"
                    :picker-options="pickerOptions0">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :xs="24" :sm="8" :md="8" :xl="7" class="mb10">
                <el-time-picker style="width: 100%" is-range v-model="item.pickTime" format='HH:mm' range-separator="至"
                  start-placeholder="开始时间" end-placeholder="结束时间" placeholder="选择时间范围" :picker-options="{
      selectableRange: '00:00:00 - 23:59:00'
    }">
                </el-time-picker>
              </el-col>
              <el-col :xs="24" :sm="8" :md="9" :xl="10">
                <el-form-item :prop="`taskData[${index}].numbers`" :rules="{required: true, message: '数量不能为空', trigger: 'change'}">
                  <el-input type="text" v-model="item.numbers" style="width: 60%" @change="allBuyNum(index)"
                    placeholder="请输入正整数"></el-input>
                  <el-button type="danger" size="small" class="delMb" @click="removeTask(item, index)" :disabled="disabled">删除
                  </el-button>
                </el-form-item>
                <div class="tipRed mt10" v-if="errorMes">数量不能超过代购总数</div>
              </el-col>
            </el-row>
          </div>
          <el-row class="mb20">
            <el-button @click="addNewTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
          </el-row>
        </div>
        <el-row>
          <el-col :span="24" :xs="24">
            <el-form-item label="产品总价">
              <span class="colTxt labels">￥{{taskForm.productTotal}}</span>
              <span class="labels col9">(产品总价) = 0 (产品价格) * 0 (数量) * 7.3 (汇率)</span>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item label="服务费">
          <span class="colTxt labels">￥{{taskForm.serviceCharge}}</span>
          <span class="labels col9">(服务费单价) * 0 (数量) + 0 (增值费单价) * 0 (数量)</span>
        </el-form-item>
        <el-form-item label="合计">
          <span class="colTxt labels">￥{{taskForm.total}}</span>
          <span class="labels col9">(产品总价)0.00 + (服务费)0.00 </span>
        </el-form-item>
        <el-form-item label="备注">
          <el-input type="textarea" v-model="taskForm.remark"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" size="medium" @click="submitPay('taskForm')">确定</el-button>
          <el-button size="medium" @click="back">返回</el-button>
        </el-form-item>
      </el-form>
    </el-col>
    <el-col :span="8" :xs="24" :sm="24" :md="24" :lg="8" class="minRight">
      <div class="tabTitle fl mt20 mb20" @click="toggleRate"><i class="el-icon-dish mr10"></i>费率</div>
      <el-table :data="rateData" border style="width: 100%" v-show="rateTab">
        <el-table-column prop="Numbers" label="币种" align="center"></el-table-column>
        <el-table-column prop="Picture" label="单位" align="center"></el-table-column>
        <el-table-column prop="CountryId" label="汇率" align="center"></el-table-column>
      </el-table>
      <div class="tabTitle zengTit mt20 mb20" @click="toggleAddFree"><i class="el-icon-coin mr10"></i>增值费</div>
      <el-table :data="addFreeData" border style="width: 100%" v-show="addFreeTab">
        <el-table-column prop="Numbers" label="产品价格区间" align="center"></el-table-column>
        <el-table-column prop="Picture" label="增值费区间" align="center"></el-table-column>
      </el-table>
      <div class="tabTitle serviceTit mt20 mb20" @click="toggleService"><i class="el-icon-guide mr10"></i>服务费</div>
      <el-table :data="serviceData" border style="width: 100%" v-show="serviceTab">
        <el-table-column prop="Numbers" label="币种" align="center"></el-table-column>
        <el-table-column prop="Picture" label="单位" align="center"></el-table-column>
        <el-table-column prop="CountryId" label="汇率" align="center"></el-table-column>
      </el-table>
    </el-col>
    </el-row>
    <el-dialog title="付款" :visible.sync="paymentModel" :close-on-click-modal="false" width="40%" :before-close="paymentClose"
      center>
      <el-form :model="paymentForm" :rules="taskRule" class="demo-ruleForm">
        <el-row :gutter="20">
          <el-col :span="11" :xs="24" :sm="11" :md="11">
            <el-form-item label="当前余额">
              <span class="tipRed">￥0</span>
            </el-form-item>
            <div>
              <el-form-item label="待付款金额">
                <span class="tipRed">￥12</span>
              </el-form-item>
            </div>
            <el-form-item label="余额支付">
              <el-input v-model="paymentForm.balancePayment"></el-input>
            </el-form-item>
            <el-form-item label="现金支付">
              <el-input v-model="paymentForm.cashPayment"></el-input>
            </el-form-item>
            <el-form-item label="付款方式">
              <el-radio-group v-model="paymentForm.paymentType">
                <el-radio label="支付宝"></el-radio>
              </el-radio-group>
            </el-form-item>
            <div>
              <el-form-item label="收款账户">
                <span>{{paymentForm.recceiveAccount}}</span>
              </el-form-item>
            </div>
            <el-form-item label="付款账号" prop="PayAccount">
              <el-input v-model="paymentForm.PayAccount"></el-input>
            </el-form-item>
            <el-form-item label="交易流水" prop="tradingFlow">
              <el-input v-model="paymentForm.tradingFlow"></el-input>
            </el-form-item>
            <div class="payTips mb20">
              <p>温馨提示</p>
              <span>1.扫码支付，请先核对支付宝收款账户是否正确;</span>
              <p>2.请填写真实有效的付款信息，连续超过3次错误，会被定义为恶意充值，会被冻结账号。</p>
            </div>
          </el-col>
          <el-col :span="10" :xs="24" :sm="10" :md="10" class="modelRight">
            <img class="payImg">
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24" :xs="24" :sm="24" :md="24" class="mt20 modelCenter">
            <el-form-item>
              <el-button type="primary">确定</el-button>
              <el-button @click="paymentClose">取消</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
  import vali from '../common/validate'
  export default {
    name: 'FbaTask',
    data() {
      return {
        disabled: true,
        paymentModel: false,
        btnTask: true,
        errorMes: false,
        nolp: true,
        serviceTab: true, // 服务费显示隐藏
        addFreeTab: true, // 服务费显示隐藏
        rateTab: true, // 服务费显示隐藏
        pickerOptions0: this.startDate(),
        rateData: [], // 费率
        addFreeData: [], // 增值费
        serviceData: [], // 服务费
        countryData: [{
            country: '美国'
          },
          {
            country: '加拿大'
          }
        ],
        paymentForm: {
          balancePayment: '',
          cashPayment: '',
          paymentType: '支付宝',
          recceiveAccount: '圭贤',
          PayAccount: '',
          tradingFlow: ''
        },
        taskForm: {
          taskData: [{
            startTime: '',
            pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
            numbers: ''
          }],
          CountryId: '',
          ProductByASIN: '',
          shopName: '',
          productName: '',
          productPrice: '',
          comments: '',
          productScore: null,
          productLink: '',
          productImg: '',
          categoryRanking: '',
          keywordTypes: '产品关键字',
          AddPlatform: '关键词加购',
          TerminalPlatform: 'PC',
          searchKeyword: '',
          buyNum: '',
          commentType: '',
          fbNumber: '',
          commentImgNum: '',
          commentVedioNum: '',
          coupon: '否',
          productTotal: '0.00',
          serviceCharge: '0.00',
          total: '0.00',
          links: '',
          remark: '',
          ygsalesnum: ''
        },
        taskRule: {
          ProductByASIN: [{
              required: true,
              message: '请输入产品ASIN',
              trigger: 'change'
            },
            {
              validator: vali.flagNum,
              trigger: 'change'
            }
          ],
          CountryId: [{
            required: true,
            message: '请输选择国家',
            trigger: 'change'
          }],
          productPrice: [{
              required: true,
              message: '请输入产品价格',
              trigger: 'change'
            },
            {
              validator: vali.proPrice,
              trigger: 'change'
            }
          ],
          productLink: [{
              required: true,
              message: '请输入产品链接',
              trigger: 'change'
            },
            {
              validator: vali.checkLink,
              trigger: 'change'
            }
          ],
          searchKeyword: [{
            required: true,
            message: '请输入关键字',
            trigger: 'change'
          }],
          buyNum: [{
              required: true,
              message: '请输入购买数量',
              trigger: 'change'
            },
            {
              validator: vali.checkNum,
              trigger: 'change'
            }
          ],
          fbNumber: [{
            required: false,
            message: '请输入关键字',
            trigger: 'change'
          }, {
            validator: vali.checkNum,
            trigger: 'change'
          }],
          commentVedioNum: [{
            validator: vali.checkNum,
            trigger: 'change'
          }],
          commentImgNum: [{
            validator: vali.checkNum,
            trigger: 'change'
          }],
          commentType: [{
            required: true,
            message: '请选择留评类型',
            trigger: 'change'
          }],
          proService: [{
              required: true,
              message: '请输入数量',
              trigger: 'change'
            },
            {
              validator: vali.checkNum,
              trigger: 'change'
            }
          ],
          tradingFlow: [{
            required: true,
            message: '请输入交易流水',
            trigger: 'change'
          }],
          PayAccount: [{
            required: true,
            message: '请输入付款账号',
            trigger: 'change'
          }]
        }
      }
    },
    created() {
      this.getDate()
    },
    methods: {
      // FB任务代购数量
      allBuyNum(index) {
        let _this = this
        _this.index = index
        let buyNum = _this.taskForm.buyNum
        let taskTime = _this.taskForm.taskData
        var result = 0
        for (let i = 0; i < taskTime.length; i++) {
          result += parseInt(taskTime[i].numbers)
        }
        if (parseInt(result) > parseInt(buyNum)) {
          _this.errorMes = true
          return false
        } else {
          _this.errorMes = false
        }
      },
      // 创建任务提交订单到支付
      submitPay(formName) {
        let _this = this
        let errorMes = _this.errorMes
        _this.$refs[formName].validate((valid) => {
          if (valid && !errorMes) {
            _this.paymentModel = true
          }
        })
      },
      // 服务费显示与隐藏
      toggleService() {
        let _this = this
        let tabs = _this.serviceTab
        if (tabs) {
          _this.serviceTab = false
        } else {
          _this.serviceTab = true
        }
      },
      // 增值费显示与隐藏
      toggleAddFree() {
        let _this = this
        let tabs = _this.addFreeTab
        if (tabs) {
          _this.addFreeTab = false
        } else {
          _this.addFreeTab = true
        }
      },
      // 汇率显示与隐藏
      toggleRate() {
        let _this = this
        let tabs = _this.rateTab
        if (tabs) {
          _this.rateTab = false
        } else {
          _this.rateTab = true
        }
      },
      // 新增一行任务
      addNewTask() {
        let _this = this
        _this.taskForm.taskData.push({
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)]
        })
        let len = _this.taskForm.taskData.length
        if (len > 1) {
          _this.disabled = false
        }
      },
      // 删除一项任务
      removeTask(item, index) {
        let _this = this
        _this.index = _this.taskForm.taskData.indexOf(item)
        let len = _this.taskForm.taskData.length
        if (index !== -1) {
          _this.taskForm.taskData.splice(index, 1)
        }
        if (len <= 2) {
          _this.disabled = true
        }
      },
      // 留评类型
      lpType() {
        let _this = this
        let lp = _this.taskForm.commentType
        if (lp === '不留评') {
          _this.nolp = false
        } else {
          _this.nolp = true
        }
      },
      //当前日期
      getDate() {
        let _this = this
        let date = new Date()
        let y = date.getFullYear()
        let m = date.getMonth() + 1
        let d = date.getDate()
        if (m < 10) {
          m = '0' + m
        }
        if (d < 10) {
          d = '0' + d
        }
        let startTime = y + '-' + m + '-' + d
        _this.taskForm.taskData[0].startTime = startTime
      },
      // 付款关闭页面
      paymentClose() {
        let _this = this
        _this.paymentModel = false
        _this.paymentForm = {
          balancePayment: '',
          cashPayment: '',
          paymentType: '支付宝',
          recceiveAccount: '圭贤',
          PayAccount: '',
          tradingFlow: ''
        }
      },
      // 返回按钮
      back() {
        let _this = this
        _this.$router.push('/loginHome')
      },
      startDate() {
        return {
          disabledDate(time) {
            return time.getTime() < Date.now() - 8.64e7
          }
        }
      },
      // 一天一单
      oneDay() {
        let _this = this
        _this.errorMes = false
        let dates = new Date()
        let y = dates.getFullYear()
        let m = dates.getMonth() + 1
        let d = dates.getDate()
        if (m < 10) {
          m = '0' + m
        }
        if (d < 10) {
          d = '0' + d
        }
        let startTime = y + '-' + m + '-' + d
        //_this.taskForm.taskData[0].startTime = startTime
        let t = _this.taskForm.taskData[0].pickTime
        let num = _this.taskForm.buyNum
        _this.taskForm.taskData = [{
          startTime: startTime,
          pickTime: t,
          numbers: ''
        }]
        if (num !== '') {
          _this.taskForm.taskData[0].numbers = '1'
          for (let i = 0; i < num - 1; i++) {
            let dateTemps = _this.taskForm.taskData[i].startTime
            var dateTemp = dateTemps.split('-')
            var nDate = new Date(dateTemp[1] + '-' + dateTemp[2] + '-' + dateTemp[0])
            var millSeconds = Math.abs(nDate) + (24 * 60 * 60 * 1000)
            var rDate = new Date(millSeconds)
            var year = rDate.getFullYear()
            var month = rDate.getMonth() + 1
            if (month < 10) month = '0' + month
            var date = rDate.getDate()
            if (date < 10) date = '0' + date
            var times = year + '-' + month + '-' + date
            _this.taskForm.taskData.push({
              startTime: times,
              pickTime: t,
              numbers: '1'
            })
          }
        }
        let len = _this.taskForm.taskData.length
        console.log(len)
        if (len > 1) {
          _this.disabled = false
        } else {
          _this.disabled = true
        }
      },
      // 一天两单
      twoDay() {
        let _this = this
        _this.errorMes = false
        let dates = new Date()
        let y = dates.getFullYear()
        let m = dates.getMonth() + 1
        let d = dates.getDate()
        if (m < 10) {
          m = '0' + m
        }
        if (d < 10) {
          d = '0' + d
        }
        let startTime = y + '-' + m + '-' + d
        let t = _this.taskForm.taskData[0].pickTime
        let num = _this.taskForm.buyNum
        _this.taskForm.taskData = [{
          startTime: startTime,
          pickTime: t,
          numbers: ''
        }]
        if (num !== '') {
          let counts = parseInt(num) / 2
          if (counts % 1 === 0) {
            for (let i = 0; i < counts - 1; i++) {
              let dateTemps = _this.taskForm.taskData[i].startTime
              let dateTemp = dateTemps.split('-')
              let nDate = new Date(dateTemp[1] + '-' + dateTemp[2] + '-' + dateTemp[0]) // 转换为MM-DD-YYYY格式
              let millSeconds = Math.abs(nDate) + (24 * 60 * 60 * 1000)
              let rDate = new Date(millSeconds)
              let year = rDate.getFullYear()
              let month = rDate.getMonth() + 1
              if (month < 10) month = '0' + month
              let date = rDate.getDate()
              if (date < 10) date = '0' + date
              let times = year + '-' + month + '-' + date
              _this.taskForm.taskData.push({
                startTime: times,
                pickTime: t,
                numbers: '2'
              })
            }
          } else {
            counts++
            let count = parseInt(counts)
            for (let i = 0; i < count - 1; i++) {
              // _this.taskData[count].numbers = 1
              let dateTemps = _this.taskForm.taskData[i].startTime
              var dateTemp = dateTemps.split('-')
              var nDate = new Date(dateTemp[1] + '-' + dateTemp[2] + '-' + dateTemp[0]) // 转换为MM-DD-YYYY格式
              var millSeconds = Math.abs(nDate) + (24 * 60 * 60 * 1000)
              var rDate = new Date(millSeconds)
              var year = rDate.getFullYear()
              var month = rDate.getMonth() + 1
              if (month < 10) month = '0' + month
              var date = rDate.getDate()
              if (date < 10) date = '0' + date
              var times = year + '-' + month + '-' + date
              _this.taskForm.taskData.push({
                startTime: times,
                pickTime: t,
                numbers: '2'
              })
            }
            let item = parseInt(count)
            _this.taskForm.taskData[item - 1].numbers = '1'
          }
          _this.taskForm.taskData[0].numbers = '2'
        }
        let len = _this.taskForm.taskData.length
        if (len > 1) {
          _this.disabled = false
        } else {
          _this.disabled = true
        }
      },
      // 一天三单
      threeDay() {
        let _this = this
        _this.errorMes = false
        let dates = new Date()
        let y = dates.getFullYear()
        let m = dates.getMonth() + 1
        let d = dates.getDate()
        if (m < 10) {
          m = '0' + m
        }
        if (d < 10) {
          d = '0' + d
        }
        let startTime = y + '-' + m + '-' + d
        let t = _this.taskForm.taskData[0].pickTime
        let num = _this.taskForm.buyNum
        _this.taskForm.taskData = [{
          startTime: startTime,
          pickTime: t,
          numbers: ''
        }]
        if (num !== '') {
          _this.taskForm.taskData[0].numbers = '3'
          let counts = parseInt(num) / 3
          if (counts % 1 === 0) {
            for (let i = 0; i < counts - 1; i++) {
              let dateTemps = _this.taskForm.taskData[i].startTime
              let dateTemp = dateTemps.split('-')
              let nDate = new Date(dateTemp[1] + '-' + dateTemp[2] + '-' + dateTemp[0]) // 转换为MM-DD-YYYY格式
              let millSeconds = Math.abs(nDate) + (24 * 60 * 60 * 1000)
              let rDate = new Date(millSeconds)
              let year = rDate.getFullYear()
              let month = rDate.getMonth() + 1
              if (month < 10) month = '0' + month
              let date = rDate.getDate()
              if (date < 10) date = '0' + date
              let times = year + '-' + month + '-' + date
              _this.taskForm.taskData.push({
                startTime: times,
                pickTime: t,
                numbers: '3'
              })
            }
          } else {
            counts++
            var count = parseInt(counts)
            for (let i = 0; i < count - 1; i++) {
              // _this.taskData[count].numbers = 1
              let dateTemps = _this.taskForm.taskData[i].startTime
              var dateTemp = dateTemps.split('-')
              var nDate = new Date(dateTemp[1] + '-' + dateTemp[2] + '-' + dateTemp[0]) // 转换为MM-DD-YYYY格式
              var millSeconds = Math.abs(nDate) + (24 * 60 * 60 * 1000)
              var rDate = new Date(millSeconds)
              var year = rDate.getFullYear()
              var month = rDate.getMonth() + 1
              if (month < 10) month = '0' + month
              var date = rDate.getDate()
              if (date < 10) date = '0' + date
              var times = year + '-' + month + '-' + date
              _this.taskForm.taskData.push({
                startTime: times,
                pickTime: t,
                numbers: '3'
              })
            }
            var item = parseInt(count)
            var result = 0
            for (let j = 0; j < item - 1; j++) {
              result += parseInt(_this.taskForm.taskData[j].numbers)
            }
            _this.taskForm.taskData[item - 1].numbers = parseInt(num) - parseInt(result)
          }
        }
        let len = _this.taskForm.taskData.length
        if (len > 1) {
          _this.disabled = false
        } else {
          _this.disabled = true
        }
      },
      // 时间范围
      timePicker() {
        let _this = this
        let pickTimes = _this.taskForm.taskData[0].pickTime
        let len = _this.taskForm.taskData.length
        for (let i = 0; i < len; i++) {
          _this.taskForm.taskData[i].pickTime = pickTimes
        }
      },
      // 代购数量为1
      checkBuyNum() {
        let _this = this
        let nums = _this.taskForm.buyNum

        _this.taskForm.taskData = [{
          startTime: '',
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
          numbers: ''
        }]
        if (parseInt(nums) === 1) {
          _this.btnTask = true
          _this.taskForm.taskData[0].numbers = 1
        } else {
          _this.btnTask = false
          _this.taskForm.taskData[0].numbers = ''
        }
        _this.getDate()
      },
    }
  }
</script>

<style scope>
.fl,.zengTit,.serviceTit{
  background: #eee;
  color: #212121;
  cursor: pointer;
}
.t-title{
  font-size: 18px;
}
</style>
