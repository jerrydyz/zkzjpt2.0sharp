<template>
  <div class="package fl">
    <div class="title">
      <span>课程包</span>
    </div>
    <div class="content">
      <ul>
        <li class="clearfix xiaoshou" v-for="(item,index) in list" :key="index" @click="gopackdetail(item.id)">
          <div class="left fl">
            <img :src="item.img_url" alt />
          </div>
          <div class="item-content fl">
            <div class="item-title">{{item.title}}</div>
            <div class="item-time clearfix">
            </div>
            <div class="total-time">学时：<span>{{Number(parseFloat(item.gongxuke_xueshi_num).toFixed(1))+Number(parseFloat(item.zhuanyeke_xueshi_num).toFixed(1))}}</span></div>
          </div>
          <div class="price-box fr">
            <div class="price"><span class="rmb">￥{{item.price}} 元</span></div>
            <div type="button" class="btn-now" @click.stop="xuexi(item.id)">{{item.isBuy=="0"?"购买":"继续学习"}}</div>
          </div>
        </li>
        

      </ul>
       <div class="blocks" style="text-align:right;margin-right:20px;margin-top:20px;">
              <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="pageNo"
                    :page-size="3"
                    layout="prev, pager, next, jumper"
                    :total="count"
                    :pager-count="7"
                    >
                </el-pagination>
            </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
import Vue from 'vue'
export default {
  name: "package",
  data (){
    return {
      list:[],
      used:10,
      id:[],
      year:'',
      msg:'购买',
      pageNo:1,
      idd:'',
      apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
       fenye:true,
       count:0,
        page:1,
        num:3,
        pageNo:1,
     
    }
  },
  created (){
    
    this.uid = localStorage.getItem("uid");
    this.token = localStorage.getItem("token");
    var date = new Date();
    this.year = date.getFullYear();
    if(this.token){
        this.kechengbao();
    }else{
         this.removeInfo()
    }
   
  },
  // mounted(){
  //     if(this.token){
  //       this.kechengbao();
  //   }else{
  //        this.removeInfo()
  //   }
  // },
  watch: {
		token: {
			handler: function(val) {
				if (val) {
          // this.kechengbao();
				}else{
           this.$router.push('/login')
           this.removeInfo()
        }
			}
		},deep:true
	},
  methods:{
     //分页
      handleCurrentChange(val) {
          this.page=val
        console.log(`当前页: ${val}`);
        this.kechengbao ()
      },
    removeInfo(){
      var that=this
      this.$message.error({message:"重新登录",duration:1600});
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
      kechengbao (){
      //获取课程包信息
       var that=this 
        this.$axios({
          method: 'post',
          url: this.apiurl+'/kecheng/get_kechengbao_list',
          data:qs.stringify({
            year:this.year,
            page:this.page,
            num:this.num
          })
          }).then(res => {
              console.log(res)
            if(res.data.status=="ok"){
               that.list=[]
               that.list=that.list.concat(res.data.data.data);
                 that.count=Number(res.data.data.count)
              //  for(var i=0;i<res.data.data.data.length;i++){
              //    that.id.push(res.data.data.data[i].id)
              //   //  that.getbaoprogress()
              //  }
              //  for(var i=0;i<that.id.length;i++){
              //      that.idd=that.id[i]
              //      console.log("单个id")
              //      console.log(that.idd)
              //     // that.getbaoprogress()
              //  }
               for(var i=0;i<that.list.length;i++){
                Vue.set(that.list[i],"isBuy","0")
                  this.isBuy(that.list[i].id)
              }
              
            }else if((res.data.status=="error")){
              this.$message.error({message:res.data.errormsg,duration:1600});
            }else if((res.data.status=="relogin")){
              that.removeInfo();
            }
      });
      },
       //各个课程包是否购买
      isBuy:function(id){
        let that =this;
          let courseId={uid:localStorage.getItem("uid"),token:localStorage.getItem("token"),kecheng_bao_id:id}
          this.$axios.post(this.apiurl+'/kecheng/check_kecheng_bao_is_buy',qs.stringify(courseId))
            .then(res => {
              console.log("李四")
              console.log(res)
              if(res.data.status=="ok"){
                 for(var i=0;i<that.list.length;i++){
                    if(res.data.data['kecheng_bao_id']==that.list[i].id){
                      Vue.set(that.list[i],"isBuy",res.data.data.check_res)
                      break
                   }
                }
                 
              }else if(res.data.status=="error"){
                this.$message.error({message: res.data.msg,duration:1600});
              }else if(res.data.status=="relogin"){
                that.removeInfo();
              }
          });
        console.log("1111111111")
        console.log(that.list)
      },
      // //各个课程包是否购买
      // isBuy:function(){
      //   let that =this;
      //   for(let i=0; i<this.list.length;i++){
      //     let courseId={uid:localStorage.getItem("uid"),token:localStorage.getItem("token"),kecheng_bao_id:this.list[i].id}
      //     this.$axios.post(this.apiurl+'/kecheng/check_kecheng_bao_is_buy',qs.stringify(courseId))
      //       .then(res => {
      //         console.log("李四")
      //         console.log(res)
      //         if(res.data.status=="ok"){
      //           if(res.data.data.check_res=="0"){
      //             this.$set(this.list[i],"checkres",'0') 
      //             // this.$router.push({path:'packagebuy',query:{packid:packageid}});
      //           }else if(res.data.data.check_res=="1"){
      //             this.$set(this.list[i],"checkres",'1') 
      //           } 
      //         }else if(res.data.status=="error"){
      //           this.$message.error({message: res.data.msg,duration:1600});
      //         }else if(res.data.status=="relogin"){
      //           that.removeInfo();
      //         }
      //     });
      //   }
      //   console.log("1111111111")
      //   console.log(that.list)
      // },
      xuexi (packageid) {
        let that =this;
        let courseId={uid:localStorage.getItem("uid"),token:localStorage.getItem("token"),kecheng_bao_id:packageid}
        this.$axios.post(this.apiurl+'/kecheng/check_kecheng_bao_is_buy',qs.stringify(courseId))
          .then(res => {
            console.log("错误信息")
            console.log(res)
            if(res.data.status=="ok"){
              if(res.data.data.check_res=="0"){
                this.$router.push({path:'packagebuy',query:{packid:packageid}});
              }else if(res.data.data.check_res=="1"){
                that.gopackdetail(packageid);
              } 
            }else if(res.data.status=="error"){
              this.$message.error({message: res.data.msg,duration:1600});
            }else if(res.data.status=="relogin"){
              that.removeInfo();
            }
            
        });
         
      },

      getbaoprogress (){
         var that=this
          that.$axios.post(this.apiurl+'/kecheng/get_kecheng_keshi_jindu',
                    qs.stringify({
                      kecheng_id:that.idd,
                      uid:that.uid,
                      token:that.token
                    })
                  ).then(res =>{
                    if(res.data.status=="ok"){
                      that.used=res.data.progress;
                    }else if((res.data.status=="error")){
                      that.used=0
                      that.msg="购买课程"
                    }else if((res.data.status=="relogin")){
                      that.removeInfo();
                    }
                  })
         
      },
      changePage (val){
        this.pageNo=val
          console.log(this.pageNo)
      },
       gopackdetail (id){
       this.$router.push({
         path:'/personalpackage',
         query:{
              codeid:id
           }
       })
    }
  }
};
</script>

<style scoped lang="less">
.package {
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
  .content {
    width: 100%;
    background-color: #fafafa;
    padding: 0 20px 20px 20px;
    ul {
      li {
        width: 100%;
        height: 142px;
        padding: 20px 0;
        border-bottom: 1px solid #c9c9c9;
        box-sizing: border-box;
        margin-bottom:10px;
        cursor: pointer;
        .left {
          margin-right: 30px;
          width: 173px;
          height: 108px;
          img {
            width: 100%;
          }
        }
        .item-content {
          width: 455px;
          height: 94px;
          vertical-align: middle;
          .item-title {
            font-size: 18px;
          }
          .item-time {
            font-size: 15px;
            color: #0a5cff;
            margin-top: 10px;
          }
          .total-time {
            font-size: 14px;
            color: #8e8e8e;
            margin-top: 10px;
          }
        }
        .price-box {
          width: 126px;
          height: 106px;
          vertical-align: middle;
          .rmb {
            font-size: 22px;
            color: #ff0000;
          }
          .btn-now {
            width: 120px;
            height: 40px;
            font-size: 16px;
            font-weight: bold;
            text-align: center;
            line-height: 40px;
            margin: 35px auto 0;
            background-color: #329df5;
            color: #fff;
            letter-spacing: 5px;
            cursor: pointer;
          }
        }
      }
    }
  }
}
</style>
