<template>
    <div class="contentView fbox">
      <div class="mb20 tabsTxt">
           <span>首页</span>
            <span>/</span>
            <span>任务管理</span>
            <span>/</span>
            <span>任务详情</span>
      </div>
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>任务信息</span>
        </div>
          <ul class="detailbox detailbox1">
            <li>
              <span>任务编码：</span>
              <p>20190611173647296133_6</p>
            </li>
            <li>
              <span>平台/国家：</span>
              <p>Amazon/美国</p>
            </li>
            <li>
              <span>任务状态：</span>
              <p>订单异常</p>
            </li>
            <li>
              <span>任务类型：</span>
              <p>不留评</p>
            </li>
            <li>
              <span>店铺名称：</span>
              <p>deto</p>
            </li>
            <li>
              <span>产品名称：</span>
              <p>手机架</p>
            </li>
            <li>
              <span>产品图片：</span>
              <p>
                <img src="" alt="" class="eval_img">
              </p>
            </li>
            <li>
              <span>品牌：</span>
              <p></p>
            </li>
            <li>
              <span>产品类型：</span>
              <p></p>
            </li>
            <li>
              <span>产品ASIN：</span>
              <p>34535353</p>
            </li>
            <li>
              <span>产品价格：</span>
              <p>$23.33</p>
            </li>
            <li>
              <span>购买数量：</span>
              <p>2</p>
            </li>
            <li>
              <span>关键词：</span>
              <p>Hands-Free Car Cell Phone Holder</p>
            </li>
            <li>
              <span>产品链接：</span>
              <a href="https://www.amazon.com/Typhoon-Hands-Free-Holder-Compatible-Samsung/dp/B07P8Q628Y/ref=sr_1_1?keywords=B07P8Q628Y&qid=1560245022&s=gateway&sr=8-1">https://www.amazon.com/Typhoon-Hands-Free-Holder-Compatible-Samsung/dp/B07P8Q628Y/ref=sr_1_1?keywords=B07P8Q628Y&qid=1560245022&s=gateway&sr=8-1</a>
            </li>
            <li>
              <span>关键字类型：</span>
              <p>产品关键字</p>
            </li>
            <li>
              <span>执行任务时间：</span>
              <p>2019-06-16</p>
            </li>
          </ul>
      </el-card>
      <el-card class="box-card mt20">
        <div slot="header" class="clearfix">
          <span>购买信息</span>
        </div>
        <ul class="detailbox detailbox1">
          <li>
            <span>购买时间：</span>
            <p>2019-06-12</p>
          </li>
          <li>
            <span>Amazon订单号：</span>
            <p>342535654</p>
          </li>
          <li>
            <span>产品金额：</span>
            <p>$34.34</p>
          </li>
          <li>
            <span>产品运费：</span>
            <p>$0.00</p>
          </li>
          <li>
            <span>产品税费：</span>
            <p>$0.00</p>
          </li>
          <li>
            <span>其他费用：</span>
            <p>$0.00</p>
          </li>
          <li>
            <span>实际支付：</span>
            <p>$23.00</p>
          </li>
        </ul>
      </el-card>
      <el-button type="primary" class="mt30" size="medium" @click="backBtn">返回</el-button>
    </div>
</template>

<script>
export default {
  name: 'viewTaskDetails',
  data () {
    return {
      form: {}
    }
  },
  methods: {
    backBtn () {
      this.$router.push('/taskManage')
    }
  }
}
</script>

<style scoped>
  .mt30{
    margin-top: 30px;
  }
  .mt20{
    margin-top: 20px;
  }
  .mb20{
    margin-bottom: 20px;
    font-size: 14px;
  }
  .contentView{
    text-align: left;
    margin: 0 auto 50px;
    position: relative;
  }
  .detailbox1 li{
    width: 48%;
    float: left;
    list-style: none;
    line-height: 36px;
    margin-bottom: 8px;
    overflow: hidden;
  }
  .detailbox1 li p{
    margin: 0;
    display: inline-block;
    color: #666;
  }
  .detailbox1 li p img{
    display: inline-block;
  }
.detailbox{
  overflow: hidden;
  margin-top: 20px;
  font-size: 14px;
}
.eval_img{
  width: 60px;
  height: 60px;
  border-radius: 4px;
  cursor: pointer;
  border: 1px solid #ccc;
  margin-right: 10px;
}
  .detailbox1{
    padding: 0;
    margin: 0 0 0 10px;
    text-align: left;
  }
  @media screen  and (max-width: 970px){
    .detailbox1 li{
      width: 100%;
    }
  }
</style>
