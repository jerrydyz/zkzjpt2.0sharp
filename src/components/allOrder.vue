<template>
  <div class="allOrder fl">
    <div class="title">
      <span>我的订单</span>
    </div>
    <div class="content">
        <div class="info clearfix">
           <span class="fl">课程信息</span>
           <span class="fl">价格</span>
           <span class="fl">支付方式</span>
           <span class="fr">订单状态</span>
        </div>
        <div class="total">
            <ul v-for="(item,index) in data" :key="index">
                <li class="clearfix dingdan">
                    <p class="fl">
                       <span>订单编号 ：</span><span>{{item.order_id}}</span> 
                    </p>
                    <p class="fr">
                    <span>下单时间 :</span><span>{{item.time}}</span>
                    </p>
                </li>
                <li class="clearfix neirong">
                    <p class="fl guanword">
                       <span>{{item.buy_obj}}</span>
                    </p>
                    <p class="fl monery">￥{{item.money}}元
                    </p>
                    <p class="fl type">{{item.pay_type=='1'?"支付宝":item.pay_type=="2"?"微信":item.pay_type=="3"?"学时卡":"赠送"}}</p>
                    <p class="fr zhifu">{{item.status=='0'?"未支付":item.status=="1"?"已支付":"支付失败"}}
                    </p>
                </li>
            </ul>
           
          <div class="block" style="text-align:right;margin-top:20px;">
            <el-pagination
                layout="prev, pager, next,jumper"
                :page-size="5" 
                :total="count"   
                @current-change="handleCurrentChange" 
                :current-page.sync="pageNo" 
                :page-count='3'
                >
            </el-pagination>
            </div>
           
           
        </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
export default {
    data (){
        return {
            uid:'',
            token:'',
            page:1,
            pageNo:1,
            num:5,
            count:0,
            data:[],
            // currentPage:1
            apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
        }
    },
    created (){
        this.uid= localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
        console.log(this.uid,this.token)
        
         if(this.token){
           this.getOrder()
            }else{
                this.$router.push('/login')
                 this.removeInfo()
            }
    },
    watch: {
		token: {
			handler: function(val) {
                 if (val) {
                    }else{
                    this.removeInfo()
                }
			}
		},deep:true
	},
    methods:{
        removeInfo(){
            var that=this
            localStorage.removeItem("uid");
            localStorage.removeItem("token");
            localStorage.removeItem("sex");
            localStorage.removeItem("name");
            localStorage.removeItem("mobile");
            localStorage.removeItem("id_card");
            localStorage.setItem("types",'rate');
            setTimeout(() => {
            that.$router.push({ path: '/login' });
            }, 1600);
        },
        getOrder (){
            var that=this
            var data={
                uid:this.uid,
                token:this.token,
                page:this.page,
                num:this.num
            }
            this.$axios.post(this.apiurl+'/pay/get_pay_order',
            qs.stringify(data)
            ).then(res =>{
                console.log(res)
                if(res.data.status=="ok"){
                    that.count=Number(res.data.data.count)
                    that.data=[]
                    that.data=that.data.concat(res.data.data.data)
                    console.log( that.data)
                }else if((res.data.status=="error")){
                    this.$message.error({message:res.data.errormsg,duration:1600});
                }else if((res.data.status=="relogin")){
                    this.$message.error({message:"重新登录",duration:1600});
                    that.removeInfo();
                }
            })
        },
       //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           localStorage.setItem('pagenum',this.page)
           this.getOrder ()
          },
    }
};
</script>

<style scoped lang="less">
.allOrder {
  width: 948px;
  .title {
    width: 100%;
    height: 55px;
    background-color: #fafafa;
    margin-bottom: 20px;
    font-size: 18px;
    padding-left: 20px;
    line-height: 55px;
    color: #0c69f5;
    box-sizing: border-box;
    span {
      border-bottom: 2px solid #0c69f5;
    }
  }
  .content{
      width: 100%;
    //   border:1px solid red;
      .info{
          width: 100%;
          height: 35px;
          line-height: 35px;
            background-color: #138bef;
            padding: 0 20px 0 20px;
            box-sizing: border-box;
            color:#fff;
            font-size:14px;
             overflow: hidden;
            span{
                display:inline-block;
                overflow: hidden;
                text-align: center;
                &:nth-child(1){
                    width: 45%;
                    text-align: left;
                }
                 &:nth-child(2){
                    width: 18%;
                }
                 &:nth-child(3){
                    width: 18%;
                   
                }
                 &:nth-child(4){
                    width: 18%;
                   text-align: right;
                }

            }
      }
      .total{
          width: 100%;
            ul{
                li{
                    &.dingdan{
                        height: 35px;
                        line-height: 35px;
                        padding: 0 20px 0 20px;
                        background-color: #f1f1f1;
                        font-size: 12px;
                        color: #333333;
                    }
                    &.neirong{
                        padding: 15px 20px 15px 20px;
                        box-sizing: border-box;
                        background-color: #fff;
                        img{
                            width: 150px;
                            margin-right:10px;
                        }
                        p{
                             width: 18%;
                             text-align: center;
                            &.guanword{
                                width: 45%;
                                text-align: left;
                                span{
                                    display: block;
                                    &:nth-child(1){
                                       width: 100%;
                                        font-size: 18px;
                                        color: #333333;
                                        overflow: hidden;
                                        text-overflow: ellipsis;
                                        white-space: nowrap;
                                    }
                                     &:nth-child(2){
                                       margin-top:49px;
                                       font-size: 14px;
                                       color: #fe0000
                                    }
                                }
                            }
                            &.monery{
                                font-size: 22px;
                                color: #fe0000;
                                 
                            }
                            &.zhifu{
                               font-size: 14px;
                                color: #fe0000; 
                                text-align: right;
                            }
                             &.type{
                               font-size: 14px;
                                color: #333; 
                            }
                        }

                    }
                }
            }
      }
  }
}
</style>
