<template>
  <div id="coupon">
    <div v-if="showContent">
      <div class="con">
        优惠券
      </div>
      <Coupons :couponData="couponData"></Coupons>
    </div>
    <Failed v-if="failedshow" :msg="failedmsg"></Failed>
    <Loading v-model="loadingshow" :text="loadtext"></Loading>
  </div>
</template>

<script>
  import 'common/css/reset.css'
  import 'common/js/config.js'
  import {isWeiXin,weixinShare} from 'common/js/common.js'
  import geturlpara from 'common/js/geturlpara.js'
  import AjaxServer from 'common/js/ajaxServer.js'
  import Failed from "components/Failed/Failed"
  import Coupons from "components/Coupons/Coupons"
  import Vue from 'vue'
  import VueResource from 'vue-resource'
  import { Loading,Group,Cell,querystring,cookie } from 'vux'
  Vue.prototype.$geturlpara=geturlpara
  Vue.use(VueResource)
  export default {
    name: 'coupon',
    data () {
      return {
        id:"",
        loadingshow: true,
        loadtext: 'loading...',
        showContent:false,
        failedshow:false,
        failedmsg:"服务请求失败，请刷新重试",
        couponData:{}
      }
    },
    components: {
      Group,
      Cell,
      Loading,
      Failed,
      Coupons
    },
    beforeCreate(){
      // 授权
      var id = this.$geturlpara.getUrlKey("id");
      console.log(id);
    },
    created () {
      var id = this.$geturlpara.getUrlKey("id");
      this.id=id;
      if (id) {
        this.fetchData(id);
      }else{
        this.loadingshow=false;
        this.failedmsg="请求参数有误"
        this.failedshow=true;
      }
    },
    mounted(){
    },
    methods: {
      //获取优惠券数据
      fetchData(id){
        var self=this;
        AjaxServer.httpGet(
          Vue,
          HOST+'/api/coupons/'+id+'.json',
          {},
          (data)=>{
            self.loadingshow=false;
            if (data.status==0) {
              self.showContent=true;
              this.couponData=data;
            }else{
              self.failedshow=true;
              self.failedmsg=data.error;
              console.log(data.error);
            }
          },
          (err)=>{
            console.log(err);
            self.loadingshow=false;
            self.failedshow=true;
          }
        );
        setTimeout(()=>{
          self.loadingshow=false;
        },10000);
      },
    }
  }
</script>

<style lang="less">
@import '~vux/src/styles/1px.less';
body{
  background-color: #f5f5f5;
  height: 100%;
}
#coupon{
  height: 100%;
  background-color: #f5f5f5;
  color: #999;
  font-size: 14px;
  .con{
    padding:10px 15px 20px;;
    img{
      width: 100%;
    }
  }
}
</style>
