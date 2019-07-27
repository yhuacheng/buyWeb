<template>
  <div class="fbox">
    <div class="page-title mb30"><span><i class='el-icon-s-order'></i></span>任务管理</div>
    <div class="searchBox">
      <el-form :model="formAll" ref="formAll" class="form-item" label-width="80px" :inline="true">
        <el-row>
          <el-col :span="5" :xs="24">
            <el-form-item label="平台" class="disInline">
              <el-radio-group v-model="formAll.resource" class="disInline">
                <el-radio label="全部"></el-radio>
                <el-radio label="Amazon"></el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="7" :xs="24">
              <el-form-item label="下单时间">
                <el-date-picker v-model="formAll.orderStartTime" type="date" placeholder="选择开始时间" :picker-options="pickerStartDate"
                  value-format="yyyy-MM-dd" style="width: 150px;"></el-date-picker>
                <el-date-picker v-model="formAll.orderEndTime" type="date" placeholder="选择结束时间" :picker-options="pickerEndDate"
                  value-format="yyyy-MM-dd" style="width: 150px;"></el-date-picker>
              </el-form-item>
          </el-col>
          <el-col :span="6" :xs="24">
              <el-form-item label="关键字" class="labelNum">
                <el-input v-model="formAll.name" style="width: 220px" placeholder="请输入关键字"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="6" :xs="24">
              <el-form-item>
                <el-button type="primary" size="medium">搜索</el-button>
                <el-button size="medium" @click="resetTask">重置</el-button>
                <el-button type="warning" size="medium" @click="exportExcel">导出</el-button>
              </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <div class="tabBox">
      <div class="tabList">
        <ul class="tabBlock">
          <li :class="active === 1 ? 'active':''" @click="getAllData()" :data-index="1">全部<span>({{allNum}})</span></li>
          <li :class="active === 3 ? 'active':''" :data-index="3" @click="daiPay">待付款<span>(0)</span></li>
          <li :class="active === 2 ? 'active':''" :data-index="2" @click="daiBuy">待购买<span>(0)</span></li>
          <li :class="active === 4 ? 'active':''" :data-index="4" @click="daifh">待发货<span>(0)</span></li>
          <li :class="active === 5 ? 'active':''" :data-index="5" @click="daish">待收货<span>(0)</span></li>
          <li :class="active === 6 ? 'active':''" :data-index="6" @click="daipj">待评价<span>(0)</span></li>
          <li :class="active === 7 ? 'active':''" :data-index="7" @click="ywc">已完成<span>(0)</span></li>
          <li :class="active === 8 ? 'active':''" :data-index="8" @click="daiCancel">已取消<span>(0)</span></li>
          <li :class="active === 9 ? 'active':''" :data-index="9" @click="errData">异常<span>(0)</span></li>
        </ul>
      </div>
      <div class="tabRight">
        <el-button type="primary" size="medium" @click="createTsk">创建任务</el-button>
      </div>
    </div>
    <div class="mt10" style="overflow-x: auto">
      <el-table :data="allOrderData" border style="width: 100%;overflow-x: auto" id="allOrder">
        <el-table-column prop="Numbers" label="任务编码" align="center" width="200">
          <template slot-scope="scope">
            <el-button type="text" @click="viewDetails(scope.$index,scope.row)">{{scope.row.Numbers}}</el-button>
          </template>
        </el-table-column>
        <el-table-column prop="CountryId" label="平台/国家" align="center" width="100"></el-table-column>
        <el-table-column prop="ProductByASIN" label="产品ASIN" align="center" width="120"></el-table-column>
        <el-table-column prop="ProductPrice" label="产品价格" align="center"></el-table-column>
        <el-table-column prop="ServiceType" label="服务类型" align="center"></el-table-column>
        <el-table-column prop="OrderNote" label="订单备注" align="center"></el-table-column>
        <el-table-column prop="Status" label="状态" align="center"></el-table-column>
        <el-table-column prop="OrderNumber" label="订单号" align="center"></el-table-column>
        <el-table-column prop="OrderTime" label="下单时间" align="center" width="200"></el-table-column>
        <el-table-column label="操作" align="center" width="250">
          <template slot-scope="scope">
            <el-button size="small" type="primary" @click="payMent" style="margin-left: 10px;" v-show="scope.row.Status=='待付款'">现在付款</el-button>
            <el-button size="small" type="warning" @click="cancelHandel" v-show="scope.row.Status=='已确认'">取消任务</el-button>
            <el-button size="small" type="success" @click="evalEdit" v-show="scope.row.Status=='已完成'">填写评价</el-button>
            <el-button size="small" type="danger" @click="delhandel" v-show="scope.row.Status=='已取消'">删除任务</el-button>
            <el-button size="small" type="primary" @click="confirmBtn" v-show="scope.row.Status=='已退款'">确认完成</el-button>
            <el-button size="small" type="danger" @click="refundBtn" v-show="scope.row.Status=='已付款'">申请退款</el-button>
            <el-button size="small" type="success" @click="cancelRefundBtn" v-show="scope.row.Status=='退款中'">取消退款</el-button>
            <el-button size="small" type="info" @click="viewDaily">查看日志</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="mt20">
        <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
          :page-sizes="[10, 20, 30, 40]" :page-size="10" layout="total, sizes, prev, pager, next, jumper" :total="allNum">
        </el-pagination>
      </div>
    </div>
    <!--任务类型-->
    <el-dialog title="任务类型" :visible.sync="taskTypeModel" width="60%" :close-on-click-modal="false" top='5vh'>
      <el-tabs v-model="taskType" @tab-click="handleClick">
        <el-tab-pane label="全部" name="all">
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">FBA任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary" @click="buyTask">立即购买</el-button>
              </div>
            </div>
          </div>
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">心愿任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary" @click="wishTask">立即购买</el-button>
              </div>
            </div>
          </div>
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">QA任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary" @click="problemTask">立即购买</el-button>
              </div>
            </div>
          </div>
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">点赞任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary" @click="likesTask">立即购买</el-button>
              </div>
            </div>
          </div>
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">购物车任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary" @click="buyCarTask">立即购买</el-button>
              </div>
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="亚马逊" name="second">
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">FBA任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary">立即购买</el-button>
              </div>
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="速卖通" name="third">
          <div class="mb20 TypeItems">
            <div>
              <div>
                <div>
                  <h4 class="titleName">亚马逊</h4>
                  <span class="titleName">FBA任务</span>
                </div>
              </div>
              <div class="des">很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
                很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份很好打公司股份
              </div>
            </div>
            <div>
              <div class="mb20 itemPrice">￥1233.00</div>
              <div>
                <el-button type="primary">立即购买</el-button>
              </div>
            </div>
          </div>
        </el-tab-pane>
      </el-tabs>
    </el-dialog>
    <!-- 创建任务-->
    <el-dialog title="创建任务" :visible.sync="addTaskModal" width="90%" custom-class="fixed-dialog" :close-on-click-modal="false" :before-close="closeModel">
      <el-row>
        <div class="mb20">产品信息</div>
      </el-row>
      <el-row>
        <el-col :span="16" :xs="24" :sm="24" :md="24" :lg="16">
          <el-form :model="taskForm" ref="taskForm" :rules="taskRule" class="demo-ruleForm" status-icon>
            <el-row>
              <el-col :span="12" :xs="24">
                <el-form-item label="国家" class="disInline minWid" prop="CountryId">
                  <el-select v-model="taskForm.CountryId" placeholder="请选择" class="disInline wid100">
                    <el-option v-for="(item,index) in countryData" :key="index" :value="index" :label="item.country"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24">
                <el-form-item label="产品ASIN" class="disInline minWid" prop="ProductByASIN">
                  <el-input v-model="taskForm.ProductByASIN" maxlength="10" show-word-limit placeholder="长度为10的数字和字母组合"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>

              <el-col :span="12" :xs="24">
                <el-form-item label="店铺名称" class="disInline minWid">
                  <el-select v-model="taskForm.shopName" placeholder="请选择" class="disInline wid100">
                    <!--<el-option></el-option>-->
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24">
                <el-form-item label="产品名称" class="disInline minWid">
                  <el-input v-model="taskForm.productName"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>

              <el-col :span="12" :xs="24">
                <el-form-item label="产品价格" class="disInline minWid" prop="productPrice">
                  <el-input type="text" v-model="taskForm.productPrice" placeholder="请输入数字">
                    <template slot="prepend">￥</template>
                  </el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24">
                <p class="lh40">产品评分</p>
                <el-form-item>
                  <el-rate v-model="taskForm.productScore"></el-rate>
                </el-form-item>
              </el-col>
            </el-row>
            <div>
              <el-col :span="12" :xs="24" class="fleft">
                <el-form-item label="评论数" class="disInline minWid">
                  <el-input type="number" v-model="taskForm.comments" :disabled="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24" class="fright">
                <el-form-item label="产品图片" class="disInline">
                  <img :src="taskForm.productImg" alt="" class="proImg">
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24" class="fleft">
                <el-form-item label="品类排名" class="wid">
                  <el-input type="text" v-model="taskForm.categoryRanking" :disabled="true"></el-input>
                </el-form-item>
              </el-col>
            </div>
            <!--<el-row>
							<el-col :span="12" :xs="24" class="fleft">
								<el-form-item label="品类排名" class="wid">
									<el-input type="text" v-model="taskForm.categoryRanking" :disabled="true"></el-input>
								</el-form-item>
							</el-col>

						</el-row>-->
            <el-row>
              <el-col :span="12" :xs="24" class="fleft">
                <el-form-item label="产品链接" class="wid" prop="productLink">
                  <el-input type="text" v-model="taskForm.productLink" placeholder="请以http://或者https://开头"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <div class="mt30 mb20">下单信息</div>
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
            </el-row>
            <el-row>
              <el-col :span="12" :xs="24">
                <el-form-item label="搜索关键词" class="disInline minWid" prop="searchKeyword">
                  <el-input v-model="taskForm.searchKeyword"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24">
                <el-form-item label="代购数量" class="disInline minWid" prop="buyNum">
                  <el-input v-model="taskForm.buyNum" @blur="checkBuyNum" placeholder="请输入正整数"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12" :xs="24">
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
              <el-col :span="12" :xs="24">
                <el-form-item label="Feedback数量" class="disInline minWid">
                  <el-input type="text" required="false" v-model="taskForm.fbNumber" placeholder="请输入正整数"></el-input>
                  <p><span class="tipRed" v-if="parseInt(this.taskForm.fbNumber)>parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.fbNumber)<0">数量必须小于等于订购数量{{taskForm.buyNum}}</span></p>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row v-show="nolp==true">
              <el-col :span="12" :xs="24">
                <el-form-item label="评论图片数量" class="disInline minWid">
                  <el-input v-model="taskForm.commentImgNum" placeholder="请输入正整数"></el-input>
                  <div class="el-form-item__error" v-if="parseInt(this.taskForm.commentImgNum) > parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.commentImgNum)>5">
                    数量不能超过订购数量并且最大为5</div>
                </el-form-item>
              </el-col>
              <el-col :span="12" :xs="24">
                <el-form-item label="评论视频数量" class="disInline minWid">
                  <el-input type="text" :required="false" v-model="taskForm.commentVedioNum" placeholder="请输入正整数"></el-input>
                  <div class="el-form-item__error" v-if="parseInt(this.taskForm.commentVedioNum) > parseInt(this.taskForm.buyNum) || parseInt(this.taskForm.commentVedioNum)>5">
                    数量不能超过订购数量并且最大为5</div>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="24" :xs="24">
                <el-form-item label="是否使用优惠券">
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
              <el-row class="mb50">
                <el-button @click="addNewTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
              </el-row>
            </div>
            <!--<el-row>
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
							<span class="labels col9">(产品总价)0.00  + (服务费)0.00 </span>
						</el-form-item>-->
            <!--<el-form-item>
							<el-button type="primary" size="medium" @click="submitPay('taskForm')">确定</el-button>
							<el-button size="medium" @click="closeModel">返回</el-button>
						</el-form-item>-->
            <!--            </el-row>-->
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

      <div slot="footer" class="dialog-footer">
        <el-form :model='taskForm' class='txtLeft pl30'>
          <el-row>
            <el-col :span="24" :xs="24">
              <span class="spanTxt">产品总价：</span>
              <span class="colTxt labels fz20">￥{{taskForm.productTotal}}</span>
              <span class="labels col9">(产品总价) = 0 (产品价格) * 0 (数量) * 7.3 (汇率)</span>
            </el-col>
          </el-row>
          <span class="spanTxt">服务费：</span>
          <span class="colTxt labels fz20">￥{{taskForm.serviceCharge}}</span>
          <span class="labels col9">(服务费单价) * 0 (数量) + 0 (增值费单价) * 0 (数量)</span>
          <div>
            <span class="spanTxt">合计：</span>
            <span class="colTxt labels fz20">￥{{taskForm.total}}</span>
            <span class="labels col9">(产品总价)0.00 + (服务费)0.00 </span>
          </div>
        </el-form>
        <el-button type="primary" size="medium" @click="submitPay('taskForm')">确定</el-button>
        <el-button size="medium" @click="closeModel">返回</el-button>
      </div>
    </el-dialog>
    <!-- 点赞任务-->
    <el-dialog title="点赞任务" :visible.sync="likesModel" width="80%" :close-on-click-modal="false" top='5vh'>
      <el-form :model="likesForm" ref="likesForm" class="demo-ruleForm" :rules="taskRule">
        <el-row>
          <el-form-item label="终端平台">
            <el-radio-group v-model="likesForm.TerminalPlatform ">
              <el-radio label="PC"></el-radio>
              <el-radio label="Android"></el-radio>
            </el-radio-group>
          </el-form-item>
        </el-row>
        <el-row>
          <el-col :span="6">
            <span>链接地址</span>
          </el-col>
          <el-col :span="7" class="center">
            <span>选项操作</span>
          </el-col>
          <el-col :span="4" class="center">
            <span>数量</span>
          </el-col>
          <el-col :span="4" class="center">
            <span>服务费</span>
          </el-col>
        </el-row>
        <div v-for="(item,index) in likesForm.likeData" :key="index" class="mt30">
          <el-row>
            <el-col :span="6" :xs="24" class="center">
              <el-form-item :prop="`likeData[${index}].address`" :rules="[{required: true, message: '链接不能为空', trigger: 'blur'},{pattern:  /^(?:http(s)?:\/\/)?[\w.-]+(?:\.[\w\.-]+)+[\w\-\._~:/?#[\]@!\$&'\*\+,;=.]+$/, message: '请以http://或https://开头', trigger: 'change'}]">
                <el-input type="text" v-model="item.address" placeholder="请以http://或https://开头"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8" :xs="24" class="center mb20">
              <el-radio-group v-model="item.selectType">
                <el-radio label="点赞"></el-radio>
                <el-radio label="点踩"></el-radio>
                <el-radio label="Report Abuse"></el-radio>
              </el-radio-group>
            </el-col>
            <el-col :span="4" :xs="24" class="center">
              <el-form-item :prop="`likeData[${index}].nums`" :rules="[{required: true, message: '数量不能为空', trigger: 'blur'},{pattern:  /^\+?[1-9][0-9]*$/, message: '数量不能小于0', trigger: 'change'}]">
                <el-input v-model="item.nums"></el-input>
              </el-form-item>

            </el-col>
            <el-col :span="3" :xs="24" class="center">
              <span class="servTxt">{{item.proService}}</span>
            </el-col>
            <el-col :span="3" :xs="24">
              <el-button type="danger" @click="removeLike(item,index)" :disabled="disabled2">删除</el-button>
            </el-col>
          </el-row>
        </div>
        <el-row class="mb120">
          <el-button @click="addLikes"><i class="el-icon-circle-plus-outline"></i>新增点赞任务</el-button>
        </el-row>
      </el-form>
      <div slot='footer' class="dialog-footer">
        <div class="txtLeft">
          <span class="spanTxt">合计：</span>
          <span class="colTxt labels fz20">￥{{likesForm.total}}</span>
        </div>
        <el-button type="primary" @click="confirLikeForm( 'likesForm')">确定</el-button>
        <el-button @click="likesModel=false">返回</el-button>
      </div>
    </el-dialog>
    <!-- 购物车任务-->
    <el-dialog title="购物车任务" :visible.sync="buyCarModel" width="60%" :close-on-click-modal="false" :before-close="closeModel"
      top='5vh'>
      <div>
        <el-row>
          <div class="mb20">产品信息</div>
        </el-row>
      </div>
      <el-form :model="taskForm" ref="buyCarForm" class="demo-ruleForm" :rules="taskRule">
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="国家" class="disInline minWid" prop="CountryId">
              <el-select v-model="taskForm.CountryId" placeholder="请选择" class="disInline wid100">
                <el-option v-for="(item,index) in countryData" :key="index" :value="index" :label="item.country"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="产品ASIN" class="disInline minWid" prop="ProductByASIN">
              <el-input v-model="taskForm.ProductByASIN" maxlength="10" show-word-limit placeholder="长度为10的数字和字母组合"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="店铺名称" class="disInline minWid">
              <el-select v-model="taskForm.shopName" placeholder="请选择" class="disInline wid100"></el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="产品名称" class="disInline minWid">
              <el-input v-model="taskForm.productName"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>

          <el-col :span="12" :xs="24">
            <el-form-item label="产品价格" class="disInline minWid" prop="productPrice">
              <el-input type="text" v-model="taskForm.productPrice" placeholder="请输入数字">
                <template slot="prepend">￥</template>
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <p class="lh40">产品评分</p>
            <el-form-item>
              <el-rate v-model="taskForm.productScore"></el-rate>
            </el-form-item>
          </el-col>

        </el-row>
        <div>
          <el-col :span="12" :xs="24" class="fleft">
            <el-form-item label="评论数" class="disInline minWid">
              <el-input v-model="taskForm.comments" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24" class="fright">
            <el-form-item label="产品图片" class="disInline">
              <img alt="" :src="taskForm.productImg" class="proImg">
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24" class="fleft">
            <el-form-item label="品类排名" class="disInline wid">
              <el-input type="text" v-model="taskForm.categoryRanking" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
        </div>
        <el-row>

          <el-col :span="12" :xs="24" class="fleft">
            <el-form-item label="产品链接" class="wid" prop="productLink">
              <el-input type="text" v-model="taskForm.productLink" placeholder="请以http://或https://开头"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>

        </el-row>
        <el-form-item label="下单信息"></el-form-item>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="加购平台">
              <el-radio-group v-model="taskForm.AddPlatform" @change="platFormCar">
                <el-radio label="关键词加购" class="mb10 mt10"></el-radio>
                <el-radio label="CPC关键词加购"></el-radio>
                <el-radio label="链接加购"></el-radio>
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
        </el-row>
        <el-row>
          <el-col :span="12" :xs="24" v-if="searchType==false">
            <el-form-item label="搜索关键词" class="disInline minWid">
              <el-input v-model="taskForm.searchKeyword"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24" v-show="searchType">
            <el-form-item label="链接" class="disInline wid">
              <el-input type="textarea" v-model="taskForm.links"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="数量" class="disInline minWid" prop="buyNum">
              <el-input v-model="taskForm.buyNum" @blur="checkBuyNum" placeholder="请输入正整数"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <div class="con">
          <el-row>
            <el-col :span="7" :xs="13" :sm="6" :md="8" :lg="6">
              <span class="mb20 disInline">任务开始时间</span>
            </el-col>
            <el-col :span="10" :xs="24" :sm="12" :md="8" :lg="8">
              <el-button type="primary" size="small" @click="oneDay" :disabled="btnTask">一天一单</el-button>
              <el-button type="primary" size="small" @click="twoDay" :disabled="btnTask">一天两单</el-button>
              <el-button type="primary" size="small" @click="threeDay" :disabled="btnTask">一天三单</el-button>
            </el-col>
            <el-col :span="6" :xs="9" :sm="6" :md="8" :lg="10" class="mt10 mb10">
              <span>数量</span>
            </el-col>
          </el-row>
          <div v-for="(item,index) in taskForm.taskData" :key="index" class="mb10">
            <el-row :gutter="20">
              <el-col :span="7" :xs="24" :sm="7" :md="8" :lg="5">
                <el-form-item :prop="`taskData[${index}].startTime`" :rules="{ required: true, message: '任务开始时间不能为空', trigger: 'change' }">
                  <el-date-picker style="width:100%;" v-model="item.startTime" type="date" placeholder="选择日期"
                    :picker-options="pickerOptions0">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="7" :xs="24" :sm="7" :md="8" :lg="7" class="mb10">
                <el-time-picker style="width: 100%;" is-range v-model="item.pickTime" format='HH:mm' range-separator="至"
                  start-placeholder="开始时间" end-placeholder="结束时间" placeholder="选择时间范围" :picker-options="{
      selectableRange: '00:00:00 - 23:59:00'
    }">
                </el-time-picker>
              </el-col>
              <el-col :span="10" :xs="24" :sm="10" :md="8" :lg="10">
                <el-form-item :prop="`taskData[${index}].numbers`" :rules="{ required: true, message: '数量不能为空', trigger: 'change' }">
                  <el-input type="text" v-model="item.numbers" style="width: 60%;" @change="allBuyNum(index)"
                    placeholder="请输入正整数"></el-input>
                  <el-button type="danger" size="small" @click="removeBuyCarTask(item, index)" :disabled="disabled">删除
                  </el-button>
                </el-form-item>
                <div class="tipRed mt10" v-if="errorMes">数量不能超过订购数量</div>
              </el-col>
            </el-row>
          </div>
          <el-row class="mb20">
            <el-button @click="addCarNewTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
          </el-row>
        </div>
        <!--<el-form-item label="服务费">
					<span class="colTxt labels">￥{{taskForm.serviceCharge}}</span>
					<span class="labels col9">(服务费单价) 0 * 0 (数量) + 0 (CPC服务费) * (数量)</span>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" size="medium" @click="buyCarComfir('buyCarForm')">确定</el-button>
					<el-button size="medium" @click="closeModel">返回</el-button>
				</el-form-item>-->
      </el-form>
      <div slot='footer' class="dialog-footer">
        <div class="txtLeft">
          <span class="spanTxt">服务费：</span>
          <span class="colTxt labels fz20">￥{{taskForm.serviceCharge}}</span>
          <span class="labels col9">(服务费单价) 0 * 0 (数量) + 0 (CPC服务费) * (数量)</span>
        </div>
        <el-button type="primary" size="medium" @click="buyCarComfir('buyCarForm')">确定</el-button>
        <el-button size="medium" @click="closeModel">返回</el-button>
      </div>
    </el-dialog>
    <!-- QA任务-->
    <el-dialog title="QA任务" :visible.sync="QAtaskModel" width="60%" :close-on-click-modal="false" top='5vh'>
      <el-form :model="QaForm" class="demo-ruleForm">
        <el-form-item label="终端类型">
          <el-radio-group v-model="QaForm.TerminalPlatform ">
            <el-radio label="PC"></el-radio>
            <el-radio label="Android"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item>
          <el-radio-group v-model="QaForm.QA" @change="selectQA">
            <el-radio label="我要提问"></el-radio>
            <el-radio label="我要回答"></el-radio>
          </el-radio-group>
        </el-form-item>
        <div class="problem" v-show="QaType===true">
          <div class="con">
            <el-form :model="problemForm" ref="problemForm">
              <el-row>
                <el-col :span="7" class="mb20">
                  <span>国家</span>
                </el-col>
                <el-col :span="7" :xs="10">
                  <span>产品ASIN</span>
                </el-col>
                <el-col :span="10" :xs="7">
                  <span>问题</span>
                </el-col>
              </el-row>
              <div v-for="(item,index) in problemForm.problemData" :key="index" class="mb20">
                <el-row :gutter="20">
                  <el-col :span="7" :xs="24" :sm="6">
                    <el-form-item :prop="`problemData[${index}].country`" :rules="[{required: true, message: '请选择国家', trigger: 'change'}]">
                      <el-select placeholder="请选择" v-model="item.country" class="wid100 mb20">
                        <el-option v-for="(item,index) in countryData" :key="index" :value="index" :label="item.country"></el-option>
                      </el-select>
                    </el-form-item>
                  </el-col>
                  <el-col :span="7" :xs="24" :sm="6">
                    <el-form-item :prop="`problemData[${index}].proAnsi`" :rules="[{required: true, message: '产品ASIN不能为空', trigger: 'change'}]">
                      <el-input v-model="item.proAnsi" maxlength="10" show-word-limit placeholder="长度为10的数字和字母组合" class="disInline wid100 mb20"></el-input>
                    </el-form-item>
                  </el-col>
                  <el-col :span="10" :xs="24" :sm="12">
                    <el-form-item :prop="`problemData[${index}].problem`" :rules="[{required: true, message: '请输入问题', trigger: 'change'}]">
                      <el-input type="textarea" v-model="item.problem" class="disInline wid"></el-input>
                      <el-button type="danger" size="small" class="delMb mt10" @click="removeProblemTask(item, index)"
                        :disabled="disabled2">删除
                      </el-button>
                    </el-form-item>
                  </el-col>
                </el-row>
              </div>
              <el-row class="mb120">
                <el-button @click="addproblemTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
              </el-row>
              <!--<el-form-item label="服务费">
								<span class="itemPrice">￥{{problemForm.serviceCharge}}</span>
							</el-form-item>
							<el-form-item>
								<el-button type="primary" @click="problemComfir('problemForm')">确定</el-button>
								<el-button @click="QAtaskModel=false">返回</el-button>
							</el-form-item>-->
              <div class="footer txtRight">
                <div class="txtLeft">
                  <span class="spanTxt">服务费：</span>
                  <span class="colTxt labels fz20">￥{{problemForm.serviceCharge}}</span>
                </div>
                <el-button type="primary" @click="problemComfir('problemForm')">确定</el-button>
                <el-button @click="QAtaskModel=false">返回</el-button>
              </div>
            </el-form>
          </div>
        </div>
        <div class="answer" v-show="QaType === false">
          <div class="con">
            <el-form :model="answerForm" ref="answerForm" status-icon>
              <el-row :gutter="20">
                <el-col :span="7" :xs="7" class="mb20">
                  <span>国家</span>
                </el-col>
                <el-col :span="7" :xs="10">
                  <span>问题链接</span>
                </el-col>
                <el-col :span="10" :xs="7">
                  <span>回答</span>
                </el-col>
              </el-row>
              <div v-for="(item,index) in answerForm.answerData" :key="index" class="mb20">
                <el-row :gutter="20">
                  <el-col :span="7" :xs="24" :sm="6">
                    <el-form-item :prop="`answerData[${index}].country`" :rules="{required: true, message: '请选择国家', trigger: 'change'}">
                      <el-select placeholder="请选择" v-model="item.country" class="wid100">
                        <el-option label="美国" value="shanghai"></el-option>
                        <el-option label="日本" value="beijing"></el-option>
                      </el-select>
                    </el-form-item>
                  </el-col>
                  <el-col :span="7" :xs="24" :sm="6">
                    <el-form-item class="wid100" :prop="`answerData[${index}].address`" :rules="{required: true, message: '请输入问题链接', trigger: 'change'}">
                      <el-input v-model="item.address"></el-input>
                    </el-form-item>
                  </el-col>
                  <el-col :span="10" :xs="24" :sm="12">
                    <el-form-item :prop="`answerData[${index}].answer`" :rules="{required: true, message: '请输入回答', trigger: 'change'}">
                      <el-input type="textarea" v-model="item.answer" class="disInline wid"></el-input>
                      <el-button type="danger" size="small" class="delMb mt10" @click="removeAnswerTask(item, index)"
                        :disabled="disabled3">删除
                      </el-button>
                    </el-form-item>
                  </el-col>
                </el-row>
              </div>
              <el-row class="mb120">
                <el-button @click="addAnswerTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
              </el-row>
              <div class="footer txtRight">
                <div class="txtLeft">
                  <span class="spanTxt">服务费：</span>
                  <span class="colTxt labels fz20">￥{{answerForm.ansServiceCharge}}</span>
                </div>
                <el-button type="primary" size="medium" @click="answerComfir('answerForm')">确定</el-button>
                <el-button size="medium" @click="QAtaskModel=false">返回</el-button>
              </div>
            </el-form>
          </div>
        </div>
      </el-form>
    </el-dialog>
    <!-- 心愿任务-->
    <el-dialog title="心愿任务" :visible.sync="wishModel" width="60%" :close-on-click-modal="false" :before-close="closeModel">
      <el-form :model="taskForm" class="demo-ruleForm" ref="wishFrom" :rules="taskRule">
        <div class="mb20">产品信息</div>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="国家" class="disInline minWid" prop="CountryId">
              <el-select v-model="taskForm.CountryId" placeholder="请选择" class="disInline wid100">
                <el-option v-for="(item,index) in countryData" :key="index" :value="index" :label="item.country"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="产品ASIN" class="disInline minWid" prop="ProductByASIN">
              <el-input v-model="taskForm.ProductByASIN" maxlength="10" show-word-limit placeholder="长度为10的数字和字母组合"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>

          <el-col :span="12" :xs="24">
            <el-form-item label="店铺名称" class="disInline minWid">
              <el-select v-model="taskForm.shopName" placeholder="请选择" class="disInline wid100">

              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="产品名称" class="disInline minWid">
              <el-input v-model="taskForm.productName"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>

          <el-col :span="12" :xs="24">
            <el-form-item label="产品价格" class="disInline minWid" prop="productPrice">
              <el-input type="text" v-model="taskForm.productPrice" placeholder="请输入数字">
                <template slot="prepend">￥</template>
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <p class="lh40">产品评分</p>
            <el-rate v-model="taskForm.productScore" show-text class="mt5"></el-rate>
          </el-col>
        </el-row>
        <div>
          <el-col :span="12" :xs="24" class="fleft">
            <el-form-item label="评论数" class="disInline minWid">
              <el-input v-model="taskForm.comments" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24" class="fright">
            <el-form-item label="产品图片" class="disInline">
              <img alt="" :src="taskForm.productImg" class="proImg">
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="品类排名" class="wid">
              <el-input type="text" v-model="taskForm.categoryRanking" :disabled="true"></el-input>
            </el-form-item>
          </el-col>
        </div>
        <el-row>
          <el-col :span="12" :xs="24" class="fleft">
            <el-form-item label="产品链接" class="wid" prop="productLink">
              <el-input type="text" v-model="taskForm.productLink" placeholder="请以http://或https://开头"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>

        </el-row>
        <el-form-item label="下单信息"></el-form-item>
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="添加方式">
              <el-radio-group v-model="taskForm.keywordTypes" @change="searchBtn">
                <el-radio label="产品关键字" class="min10"></el-radio>
                <el-radio label="CPC关键字" class="min10"></el-radio>
                <el-radio label="链接"></el-radio>
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
        </el-row>
        <el-row>
          <el-col :span="12" :xs="24" v-show="searchType===false">
            <el-form-item label="搜索关键词" class="disInline minWid">
              <el-input v-model="taskForm.searchKeyword"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24" v-show="searchType">
            <el-form-item label="链接" class="disInline wid">
              <el-input type="textarea" v-model="taskForm.links"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12" :xs="24">
            <el-form-item label="代购数量" class="disInline minWid" prop="buyNum">
              <el-input type="text" v-model="taskForm.buyNum" @change="checkBuyNum" placeholder="请输入正整数"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <div class="con">
          <el-row :gutter="20">
            <el-col :span="7" :xs="12" :md="8" :sm="6" :lg="8" :xl="8">
              <span class="mb20 disInline">任务开始时间</span>
            </el-col>
            <el-col :span="10" :md="10" :sm="12" :xs="24" :lg="8" :xl="8">
              <el-button type="primary" size="small" @click="oneDay" :disabled="btnTask">一天一单</el-button>
              <el-button type="primary" size="small" @click="twoDay" :disabled="btnTask">一天两单</el-button>
              <el-button type="primary" size="small" @click="threeDay" :disabled="btnTask">一天三单</el-button>
            </el-col>
            <el-col :span="7" :md="6" :sm="6" :lg="8" :xl="8" class="mb10">
              <span>数量</span>
            </el-col>
          </el-row>
          <div v-for="(item,index) in taskForm.taskData" :key="index" class="mb10">
            <el-row :gutter="20">
              <el-col :span="7" :xs="24" :md="8" :sm="8">
                <el-form-item :prop="`taskData[${index}].startTime`" :rules="{ required: true, message: '任务时间不能为空', trigger: 'change' }">
                  <el-date-picker style="width: 100%" v-model="item.startTime" type="date" placeholder="选择日期"
                    :picker-options="pickerOptions0">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="10" :xs="24" :md="8" :sm="8" class="mb10">
                <el-time-picker style="width: 100%" is-range v-model="item.pickTime" format='HH:mm' range-separator="至"
                  start-placeholder="开始时间" end-placeholder="结束时间" placeholder="选择时间范围" :picker-options="{
      selectableRange: '00:00:00 - 23:59:00'
    }">
                </el-time-picker>
              </el-col>
              <el-col :span="7" :xs="24" :md="8" :sm="8">
                <el-form-item :prop="`taskData[${index}].numbers`" :rules="{ required: true, message: '数量不能为空', trigger: 'change' }">
                  <el-input type="text" v-model="item.numbers" style="width: 60%;" placeholder="请输入正整数" @blur="allBuyNum(index)"></el-input>
                  <el-button type="danger" size="small" class="delMb" @click="removeWishTask(item, index)" :disabled="disabled">删除
                  </el-button>
                </el-form-item>

                <div class="tipRed mt10" v-if="errorMes">数量不能超过代购数量</div>
              </el-col>
            </el-row>
          </div>
          <el-row class="mt20 mb20">
            <el-button @click="addWishTask"><i class="el-icon-circle-plus-outline"></i>添加新任务</el-button>
          </el-row>
        </div>
        <!--<el-form-item label="服务费">
					<span class="colTxt labels">￥{{taskForm.serviceCharge}}</span>
					<span class="labels col9">(服务费单价) * 0 (数量) + 0 (增值费单价) * 0 (数量)</span>
				</el-form-item>
				<el-form-item style="display: block">

				</el-form-item>-->
      </el-form>
      <div slot='footer' class="dialog-footer">
        <div class="txtLeft">
          <span class="spanTxt">合计：</span>
          <span class="colTxt labels fz20">￥{{taskForm.serviceCharge}}</span>
          <span class="labels col9">(服务费单价) * 0 (数量) + 0 (增值费单价) * 0 (数量)</span>
        </div>
        <el-button type="primary" size="medium" @click="wishComfir('wishFrom')">确定</el-button>
        <el-button @click="closeModel" size="medium">返回</el-button>
      </div>
    </el-dialog>
    <!-- 填写评价-->
    <el-dialog title="评价" :visible.sync="assessModel" width="30%" :close-on-click-modal="false" center="">
      <el-form :model="assessForm">
        <el-form-item label="产品评价星级">
          <el-rate v-model="assessForm.assessValue"></el-rate>
        </el-form-item>
        <el-form-item label="产品评价标题">
          <el-input v-model="assessForm.proTitle" class="wid"></el-input>
        </el-form-item>
        <el-form-item label="产品评价内容">
          <el-input type="textarea" v-model="assessForm.proCon" class="wid"></el-input>
        </el-form-item>
        <el-form-item class='txtCenter'>
          <p>评论图片 可上传图片数1，（单张图片大小不应超过5M）
          </p>
          <el-upload action="https://jsonplaceholder.typicode.com/posts/" list-type="picture-card" :on-preview="handlePictureCardPreview"
            :on-remove="handleRemove">
            <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        </el-form-item>
        <el-form-item class="center">
          <el-button type="primary" size="medium">确认</el-button>
          <el-button @click="assessModel=false" size="medium">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    <!-- 日志-->
    <el-dialog title="日志" :visible.sync="dailyModel" :close-on-click-modal="false">
      <el-timeline :reverse="reverse">
        <el-timeline-item v-for="(activity, index) in activities" :key="index" :timestamp="activity.timestamp">
          {{activity.content}}
        </el-timeline-item>
      </el-timeline>
    </el-dialog>
    <!-- 删除-->
    <el-dialog title="温馨提示" :visible.sync="delModel" :close-on-click-modal="false" center="" width="30%">
      <div class="del-dialog-cnt">是否删除此数据?</div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="medium">确定</el-button>
        <el-button @click="delModel=false" size="medium">取消</el-button>
      </span>
    </el-dialog>
    <!--确认-->
    <el-dialog title="温馨提示" :visible.sync="submitModal" :close-on-click-modal="false" center="" width="30%">
      <div class="del-dialog-cnt">是否确认此订单?</div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="medium">确定</el-button>
        <el-button @click="submitModal=false" size="medium">取消</el-button>
      </span>
    </el-dialog>
    <!--申请退款-->
    <el-dialog title="温馨提示" :visible.sync="refundModal" :close-on-click-modal="false" center="" width="30%">
      <div class="del-dialog-cnt">是否确定申请退款?</div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="medium">确定</el-button>
        <el-button @click="refundModal=false" size="medium">取消</el-button>
      </span>
    </el-dialog>
    <!--取消退款-->
    <el-dialog title="温馨提示" :visible.sync="cancelRefundModal" :close-on-click-modal="false" center="" width="30%">
      <div class="del-dialog-cnt">是否确定取消退款申请?</div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="medium">确定</el-button>
        <el-button @click="cancelRefundModal=false" size="medium">取消</el-button>
      </span>
    </el-dialog>
    <!--取消-->
    <el-dialog title="取消订单" :visible.sync="cancelModal" :close-on-click-modal="false" center="" width="30%">
      <el-input placeholder='请输入取消原因' v-model='reasonTxt'></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="medium">确定</el-button>
        <el-button @click="cancelModal=false" size="medium">取消</el-button>
      </span>
    </el-dialog>
    <!-- 付款-->
    <el-dialog title="付款" :visible.sync="paymentModel" :close-on-click-modal="false" width="40%" :before-close="paymentClose"
      top='5vh'>
      <el-form :model="paymentForm" :rules="taskRule" class="demo-ruleForm">
        <el-row>
          <el-col :span="12" :xs="24">
            <el-form-item label="当前余额">
              <span class="tipRed">￥0</span>
            </el-form-item>
            <div>
              <el-form-item label="待付款金额">
                <span class="tipRed">￥12</span>
              </el-form-item>
            </div>
            <!--<el-form-item label="现金支付">
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
						</el-form-item>-->
            <!--<div class="payTips mb20">
							<p>温馨提示</p>
							<span>1.扫码支付，请先核对支付宝收款账户是否正确;</span>
							<p>2.请填写真实有效的付款信息，连续超过3次错误，会被定义为恶意充值，会被冻结账号。</p>
						</div>-->
          </el-col>
          <!--<el-col :span="10" :xs="24" :sm="10" :md="10" class="modelRight">
						<img class="payImg">
					</el-col>-->
        </el-row>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary">确定</el-button>
        <el-button @click="paymentClose">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import vali from '../common/validate'
  import FileSaver from 'file-saver'
  import XLSX from 'xlsx'
  export default {
    name: 'taskManage',
    data() {
      return {
        dialogImageUrl: '',
        dialogVisible: false,
        loading: true,
        status: this.$route.params.taskTypeModel,
        errorStatus: this.$route.params.active,
        pageSize: '0',
        btnTask: false,
        currentPage: 1,
        cancelModal: false,
        delModel: false, // 删除
        reverse: true,
        paymentModel: false,
        submitModal: false, //确认
        refundModal: false, //申请退款
        cancelRefundModal: false, //取消退款
        reasonTxt: '', //取消原因
        activities: [{
          content: '已发货',
          timestamp: '2019-04-15'
        }, {
          content: '已付款',
          timestamp: '2019-04-13'
        }, {
          content: '已创建',
          timestamp: '2019-04-11'
        }],
        active: 1,

        QaType: true,
        allNum: 0,
        dailyModel: false,
        searchType: false,
        disabled: true,
        disabled2: true,
        disabled3: true,
        likesModel: false,
        buyCarModel: false,
        QAtaskModel: false,
        wishModel: false,
        assessModel: false,
        addTaskModal: false,
        taskTypeModel: false,
        errorMes: false,
        nolp: true,
        startTime: '',
        buyNumData: [],
        pickerOptions0: this.startDate(),
        pickerEndDate: this.pickerOptionsEnd(),
        pickerStartDate: this.searchStartDate(),
        rateData: [], // 费率
        addFreeData: [], // 增值费
        serviceData: [], // 服务费
        serviceTab: false, // 服务费显示隐藏
        addFreeTab: false, // 服务费显示隐藏
        rateTab: true, // 服务费显示隐藏
        paymentForm: {
          balancePayment: '',
          cashPayment: '',
          paymentType: '支付宝',
          recceiveAccount: '圭贤',
          PayAccount: '',
          tradingFlow: ''
        },
        countryData: [{
            country: '美国'
          },
          {
            country: '加拿大'
          }
        ],
        formAll: {
          resource: '全部',
          name: '',
          orderStartTime: '',
          orderEndTime: ''
        },
        assessForm: {
          assessValue: null,
          proTitle: '',
          proCon: ''
        },
        problemForm: {
          serviceCharge: '0.00',
          problemData: [{
            country: '',
            proAnsi: '',
            problem: '',
          }]
        },
        answerForm: {
          ansServiceCharge: '0.00',
          answerData: [{
            country: '',
            address: '',
            answer: '',
          }]
        },

        taskData: [{
          startTime: '',
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
          numbers: ''
        }],
        buyCarData: [{
          startTime: '',
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
          numbers: ''
        }],
        wishData: [{
          startTime: '',
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
          numbers: ''
        }],
        activeName: 'first',
        taskType: 'all',
        //				tableData: [],
        allOrderData: [{
            "Numbers": "20190605105636229596",
            "Picture": "",
            "CountryId": "美国",
            "Forum": "Amazon",
            "ProductByASIN": "777888999a",
            "ProductPrice": 15.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "待付款",
            "OrderNumber": 1314520,
            "OrderTime": "2019-02-03T00:00:00",
            "Remark": ""
          },
          {
            "Numbers": "20190611174157617041",
            "Picture": "",
            "CountryId": "德国",
            "Forum": "Amazon",
            "ProductByASIN": "B07P6KVGF8",
            "ProductPrice": 18.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "已付款",
            "OrderNumber": 7758258,
            "OrderTime": "2019-04-02T00:00:00",
            "Remark": ""
          },
          {
            "Numbers": "20190611174157617041",
            "Picture": "",
            "CountryId": "德国",
            "Forum": "Amazon",
            "ProductByASIN": "B07P6KVGF8",
            "ProductPrice": 18.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "已确认",
            "OrderNumber": 7758258,
            "OrderTime": "2019-04-02T00:00:00",
            "Remark": ""
          },
          {
            "Numbers": "20190611174157617041",
            "Picture": "",
            "CountryId": "日本",
            "Forum": "Amazon",
            "ProductByASIN": "B07P6KVGF8",
            "ProductPrice": 18.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "已取消",
            "OrderNumber": 7758258,
            "OrderTime": "2019-04-02T00:00:00",
            "Remark": ""
          },
          {
            "Numbers": "20190611174157617041",
            "Picture": "",
            "CountryId": "德国",
            "Forum": "Amazon",
            "ProductByASIN": "B07P6KVGF8",
            "ProductPrice": 18.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "已退款",
            "OrderNumber": 7758258,
            "OrderTime": "2019-04-02T00:00:00",
            "Remark": ""
          },
          {
            "Numbers": "20190611174157617041",
            "Picture": "",
            "CountryId": "德国",
            "Forum": "Amazon",
            "ProductByASIN": "B07P6KVGF8",
            "ProductPrice": 18.99,
            "ServiceType": "不留评",
            "OrderNote": "哇哈哈哈哈哈",
            "Status": "已完成",
            "OrderNumber": 7758258,
            "OrderTime": "2019-04-02T00:00:00",
            "Remark": ""
          }
        ],
        likesForm: {
          likeData: [{
            proService: '33',
            address: '',
            selectType: '',
            nums: ''
          }],
          total: '0.00',
          TerminalPlatform: 'PC'

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
          remark: ''
        },
        buyCarForm: {
          CountryId: '',
          ProductByASIN: '',
          shopName: '',
          productName: '',
          productPrice: '',
          comments: '',
          productScore: null,
          productLink: '',
          productImg: '',
          AddPlatform: '关键词加购',
          TerminalPlatform: 'PC',
          buyNum: '',
          searchKeyword: '',
          serviceCharge: '',
          categoryRanking: '',
          links: ''
        },
        // wishForm: {
        //   CountryId: '',
        //   ProductByASIN: '',
        //   shopName: '',
        //   productName: '',
        //   productPrice: '',
        //   comments: '',
        //   productScore: null,
        //   productLink: '',
        //   productImg: '',
        //   AddPlatform: '产品关键字',
        //   TerminalPlatform: 'PC',
        //   buyNum: '',
        //   searchKeyword: '',
        //   serviceCharge: '',
        //   categoryRanking: '',
        //   links: ''
        // },
        QaForm: {
          TerminalPlatform: 'PC',
          QA: '我要提问'
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
      this.getAllData()
      this.getTaskLeng()
      this.getDate()
      this.getError()
      let status = this.status
      if (status) {
        this.taskTypeModel = true
      } else {
        this.taskTypeModel = false
      }
    },
    methods: {
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      //列表确认付款
      payMent() {
        let _this = this
        _this.paymentModel = true
      },
      //申请退款
      refundBtn() {
        let _this = this
        _this.refundModal = true
      },
      //确认
      confirmBtn() {
        let _this = this
        _this.submitModal = true
      },
      // 任务管理全部状态信息
      getAllData() {
        let _this = this
        _this.active = 1
        _this.axios.get(_this.GLOBAL.BASE_URL + 'api/OrderManagement/AddOrderByType').then((res) => {
          _this.allOrderData = res.data.data
          _this.allNum = res.data.data.length
          _this.loading = false
        }).catch((error) => {
          console.log(error)
        })
      },
      // 获取异常
      getError() {
        let _this = this
        let errors = _this.errorStatus
        if (errors != undefined) {
          this.errData()
        }
      },
      // 点赞任务提交
      confirLikeForm(formName) {
        let _this = this
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            _this.paymentModel = true
            _this.likesModel = false

          } else {
            return false

          }
        })
      },
      // 重置
      resetTask() {
        let _this = this
        //				 this.$refs[formName].resetFields()
        _this.formAll = {
          resource: '全部',
          name: '',
          orderStartTime: '',
          orderEndTime: ''
        }
      },
      // 导出
      exportExcel() {
        var xlsxParam = {
          raw: true
        }
        // 导出的内容只做解析，不进行格式转换
        var wb = XLSX.utils.table_to_book(document.querySelector('#allOrder'), xlsxParam)
        var wbout = XLSX.write(wb, {
          bookType: 'xlsx',
          bookSST: true,
          type: 'array'
        })
        try {
          FileSaver.saveAs(new Blob([wbout], {
            type: 'application/octet-stream'
          }), '任务订单表.xlsx')
        } catch (e) {
          if (typeof console !== 'undefined') {
            console.log(e, wbout)
          }
        }
        return wbout
      },
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`)
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`)
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
        _this.taskData[0].startTime = startTime
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
      wishCheckBuyNum() {
        let _this = this
        let nums = _this.taskForm.buyNum
        if (parseInt(nums) === 1) {
          _this.btnTask = true
          _this.taskForm.taskData[0].numbers = 1
        } else {
          _this.btnTask = false
          _this.taskForm.taskData[0].numbers = ''
        }
      },
      checkBuyCarNum() {
        let _this = this
        let nums = _this.buyCarForm.buyNum
        if (parseInt(nums) === 1) {
          _this.buyCarData[0].numbers = 1
        } else {
          _this.buyCarData[0].numbers = ''
        }
      },
      // 付款关闭页面
      paymentClose() {
        let _this = this
        _this.paymentModel = false
        _this.taskForm = {
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
          taskData: [{
            startTime: '',
            pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
            numbers: ''
          }]
        }
      },
      // 创建任务提交订单到支付
      submitPay(formName) {
        let _this = this
        let errorMes = _this.errorMes
        _this.$refs[formName].validate((valid) => {
          if (valid && !errorMes) {
            _this.paymentModel = true
            _this.addTaskModal = false
            _this.likesModel = false
            _this.buyCarModel = false
            _this.QAtaskModel = false
            _this.wishModel = false
          }
        })
      },
      //购物车任务提交
      buyCarComfir(formName) {
        let _this = this
        let errorMes = _this.errorMes
        _this.$refs[formName].validate((valid) => {
          if (valid && !errorMes) {
            _this.paymentModel = true
            _this.addTaskModal = false
            _this.likesModel = false
            _this.buyCarModel = false
            _this.QAtaskModel = false
            _this.wishModel = false
          }
        })
      },
      //心愿订单任务提交
      wishComfir(formName) {
        let _this = this
        let errorMes = _this.errorMes
        _this.$refs[formName].validate((valid) => {
          if (valid && !errorMes) {
            _this.paymentModel = true
            _this.addTaskModal = false
            _this.likesModel = false
            _this.buyCarModel = false
            _this.QAtaskModel = false
            _this.wishModel = false
          }
        })
      },
      // QA任务问题提交
      problemComfir(formName) {
        let _this = this
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            _this.paymentModel = true
            _this.QAtaskModel = false
          }
        })
      },
      // QA任务回答提交
      answerComfir(formName) {
        let _this = this
        _this.$refs[formName].validate((valid) => {
          if (valid) {
            _this.paymentModel = true
            _this.QAtaskModel = false
          }
        })
      },
      // 当前日期
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
        _this.buyCarData[0].startTime = startTime
        _this.wishData[0].startTime = startTime
      },
      // 取消
      cancelHandel() {
        let _this = this
        _this.cancelModal = true
      },
      // 删除
      delhandel() {
        let _this = this
        _this.delModel = true
      },
      //取消退款
      cancelRefundBtn() {
        let _this = this
        _this.cancelRefundModal = true
      },
      // 日志
      viewDaily() {
        let _this = this
        _this.dailyModel = true
      },
      // 待购买
      daiBuy() {
        let _this = this
        _this.active = 2
        _this.allOrderData = []
      },
      //待付款
      daiPay() {
        let _this = this
        _this.active = 3
        _this.allOrderData = []
      },
      // 待发货
      daifh() {
        let _this = this
        _this.active = 4
        _this.allOrderData = []
      },
      // 待收货
      daish() {
        let _this = this
        _this.active = 5
        _this.allOrderData = []
      },
      // 待评价
      daipj() {
        let _this = this
        _this.active = 6
        _this.allOrderData = []
      },
      // 已完成
      ywc() {
        let _this = this
        _this.active = 7
        _this.allOrderData = []
      },
      // 已取消
      daiCancel() {
        let _this = this
        _this.active = 8
        _this.allOrderData = []
      },
      // 异常
      errData() {
        let _this = this
        _this.active = 9
        _this.allOrderData = []
      },
      searchStartDate() {
        return {
          disabledDate: time => {
            let endDateVal = this.formAll.orderEndTime
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
            let beginDateVal = this.formAll.orderStartTime
            if (beginDateVal) {
              return (
                time.getTime() <
                new Date(beginDateVal).getTime() - 1 * 24 * 60 * 60 * 1000
              )
            }
          }
        }
      },
      endTimeStatus: function(value) {
        this.orderEndTime = value
      },
      // FB任务弹窗返回按钮
      closeModel() {
        let _this = this
        _this.addTaskModal = false
        _this.disabled = true
        _this.buyCarModel = false
        _this.wishModel = false
        _this.taskForm = {
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
          taskData: [{
            startTime: '',
            pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
            numbers: ''
          }]
        }
        _this.getDate()
        //				_this.taskData = [{
        //					startTime: '',
        //					pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
        //					numbers: ''
        //				}]
      },
      // 心愿任务关闭弹窗
      closeModelWish() {
        let _this = this
        _this.wishModel = false
        _this.disabled = true
        _this.taskForm = {
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
          taskData: [{
            startTime: '',
            pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
            numbers: ''
          }]
        }
        //				_this.taskData = [{
        //					startTime: '',
        //					pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)],
        //					numbers: ''
        //				}]
      },
      // 购物车加购平台选择
      platFormCar() {
        let _this = this
        let forms = _this.taskForm.AddPlatform
        if (forms === '链接加购') {
          _this.searchType = true
        } else {
          _this.searchType = false
        }
      },
      //  购物车任务代购数量
      buyCheckNum(index) {
        let _this = this
        _this.index = index
        let buyNum = _this.buyCarForm.buyNum
        let taskTime = _this.buyCarData
        var result = 0
        for (let i = 0; i < taskTime.length; i++) {
          result += parseInt(taskTime[i].numbers)
        }
        if (result > buyNum) {
          _this.errorMes = true
          return false
        } else {
          _this.errorMes = false
        }
      },
      // 心愿任务代购数量
      checkNums(index) {
        let _this = this
        _this.index = index
        let buyNum = _this.wishForm.buyNum
        let taskTime = _this.wishData
        var result = 0
        for (let i = 0; i < taskTime.length; i++) {
          result += parseInt(taskTime[i].numbers)
        }
        if (result > buyNum) {
          _this.errorMes = true
          return false
        } else {
          _this.errorMes = false
        }
      },
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
        console.log(result)
        if (parseInt(result) > parseInt(buyNum)) {
          _this.errorMes = true
          return false
        } else {
          _this.errorMes = false
        }
      },
      // 心愿添加方式
      searchBtn() {
        let _this = this
        let wish = _this.taskForm.keywordTypes
        if (wish === '链接') {
          _this.searchType = true
        } else {
          _this.searchType = false
        }
      },
      // 填写评价
      evalEdit() {
        let _this = this
        _this.assessModel = true
      },
      // 查看任务详情
      viewDetails() {
        this.$router.push('/viewTaskDetails')
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
      // 心愿任务弹窗
      wishTask() {
        let _this = this
        _this.wishModel = true
      },
      // 选择QA任务类型
      selectQA() {
        let _this = this
        let qa = _this.QaForm.QA
        if (qa === '我要提问') {
          _this.QaType = true
        } else if (qa === '我要回答') {
          _this.QaType = false
        }
      },
      // QA任务弹窗
      problemTask() {
        let _this = this
        _this.QAtaskModel = true
      },
      // QA任务我要回答新增一行
      addAnswerTask() {
        let _this = this
        _this.answerForm.answerData.push({
          country: '',
          proAsin: '',
          problem: ''
        })
        let len = _this.answerForm.answerData.length
        if (len > 1) {
          _this.disabled3 = false
        }
      },
      // QA任务我要回答删除一行
      removeAnswerTask(item, index) {
        let _this = this
        _this.index = _this.answerForm.answerData.indexOf(item)
        let len = _this.answerForm.answerData.length
        if (index !== -1) {
          _this.answerForm.answerData.splice(index, 1)
        }
        if (len <= 2) {
          _this.disabled3 = true
        }
      },
      // 购物车任务
      buyCarTask() {
        let _this = this
        _this.buyCarModel = true
      },
      // 新增一行点赞任务
      addLikes() {
        let _this = this
        _this.likesForm.likeData.push({
          proService: '22',
          address: '',
          selectType: '',
          nums: ''
        })
        let len = _this.likesForm.likeData.length
        if (len > 1) {
          _this.disabled2 = false
        }
      },
      // 删除一行点赞
      removeLike(item, index) {
        let _this = this
        _this.index = _this.likesForm.likeData.indexOf(item)
        let len = _this.likesForm.likeData.length
        if (index !== -1) {
          _this.likesForm.likeData.splice(index, 1)
        }
        if (len <= 2) {
          _this.disabled2 = true
        }
      },
      getTaskLeng() {
        let _this = this
        let len = _this.taskData.length
        if (len === 1) {
          _this.disabled = true
        }
      },
      // QA任务我要提问新增一行
      addproblemTask() {
        let _this = this
        _this.problemForm.problemData.push({
          country: '',
          proAsin: '',
          problem: ''
        })
        let len = _this.problemForm.problemData.length
        if (len > 1) {
          _this.disabled2 = false
        }
      },
      // QA任务删除一行
      removeProblemTask(item, index) {
        let _this = this
        _this.index = _this.problemForm.problemData.indexOf(item)
        let len = _this.problemForm.problemData.length
        if (index !== -1) {
          _this.problemForm.problemData.splice(index, 1)
        }
        if (len <= 2) {
          _this.disabled2 = true
        }
      },
      // 心愿任务新增一行
      addWishTask() {
        let _this = this
        _this.taskForm.taskData.push({
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)]
        })
        let len = _this.taskForm.taskData.length
        if (len > 1) {
          _this.disabled = false
        }
      },
      // 心愿任务删除一行
      removeWishTask(item, index) {
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
      // 购物车任务新增一行
      addCarNewTask() {
        let _this = this
        _this.taskForm.taskData.push({
          pickTime: [new Date(2016, 9, 10, 0, 0), new Date(2016, 9, 10, 23, 59)]
        })
        let len = _this.taskForm.taskData.length
        if (len > 1) {
          _this.disabled = false
        }
      },
      // 购物车任务删除一行
      removeBuyCarTask(item, index) {
        let _this = this
        _this.index = _this.taskForm.taskData.indexOf(item)
        let len = _this.taskForm.taskData.length
        console.log(len)
        if (index !== -1) {
          _this.taskForm.taskData.splice(index, 1)
        }
        if (len <= 2) {
          _this.disabled = true
        }
      },
      startDate() {
        return {
          disabledDate(time) {
            return time.getTime() < Date.now() - 8.64e7
          }
        }
      },
      handleClick(tab, event) {
        console.log(tab, event)
      },
      // 任务类型弹窗
      createTsk() {
        let _this = this
        _this.taskTypeModel = true
      },
      // 购物车任务弹窗
      buyTask() {
        let _this = this
        _this.addTaskModal = true
      },
      // 点赞任务弹窗
      likesTask() {
        let _this = this
        _this.likesModel = true
      }
    }
  }
</script>

<style scoped>
  .footer {
    position: absolute;
    width: 100%;
    bottom: 0;
    left: 0;
    right: 0;
    background: #eee;
    padding: 20px;
    box-sizing: border-box;
  }
</style>
