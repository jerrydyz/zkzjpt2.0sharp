<template>
<div class="my">
    <div class="title">
      <div class="top w clearfix">
        <p class="fl">周口市专业技术人员继续教育协会</p>
        <p class="fr xiaoshou">
          <span class="spn1" @click="personal" >个人中心</span>
          <span @click="goback">退出</span>
        </p>
      </div>
    </div>
  <div class="packagedetail">
    <div class="topbox coursebg">
      <div class="wrap">
        <div class="coursetopbox">
          <div class="left">
            <img
              :src="imgs"
              alt="国际商务与国际营销(43课时)"
            />
          </div>

          <div class="right">
            <h3>{{title}}({{xueshi}}课时)</h3>
            <div class="coursetopitem">可得学时：{{xueshi}}学时</div>
            <!-- <div class="coursetopitem">适合区域：河南省</div> -->
            <div class="coursetopitem">年份：{{year}}</div>
            <div class="coursetopitem">
              <div class="fl">价格：</div>
              <div class="new_price">
                <span>¥{{price}}</span>
              </div>
              
            </div>
           
            <div class="coursetopitem btn">
              <!-- <span id="charge_video" class="buy" @click="gopackdetail">购买</span> -->
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="content">
      <div class="wrap course_album">
        <div class="coursemain">
          <div class="coursemain-hd" id="view_main_nav">
            <ul>
              <li class="on">
                <i class="icon-02"></i>套餐课程目录(3)
              </li>
            </ul>
          </div>

          <!-- 班级简介 -->
          <div class="coursemain-bd" style="display:none;">
            <div class="coursemain_con">国际商务与国际营销国际商务与国际营销国际商务与国际营销国际商务与国际营销国际商务与国际营销</div>
          </div>
          <!-- 班级包含课程 -->
          <div class="coursemain-bd" style="display: block;">
            <div class="coursemain_con">
              <ul class="coursemainlist">
                <li v-for="item in data" :key="item.id" @click="coursedetail(item.id)">
                    <img
                      :src="item.img_url"
                    />
                  <h3>{{item.title}}</h3>
                  <p class="p_price">价格：{{item.price}}元</p>
                  <div class="info">课程学时：{{item.xueshi_num}}学时</div>
                  <p><span>主讲:&nbsp;</span><span>{{item.jiangshi.name}}</span></p>
                </li>
              </ul>
            </div>
          </div>
          <!-- 班级评论 -->
        </div>
      </div>
      <div class="clear"></div>
    </div>
  </div>
  <foot></foot>
</div>
</template>

<script>
import qs from 'qs'
import foot from '@/components/footer';
export default {
  name:'personalpackage',
  components:{
      foot,
  },
  data() {
    return {
        codeid:'',
        data:[],
        price:'',
        xueshi:'',
        year:'',
        imgs:'',
        title:'',
        idd:'',
        token:localStorage.getItem("token"),
        apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
    };
  },
  
  created() {
    if(localStorage.getItem("token")){
      this.codeid=this.$route.query.codeid
      console.log(this.codeid)
      this.getkechengbao()
    }else{
       that.removeInfo();
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
  methods: {
      getkechengbao (){
          var that=this
          this.$axios.post(this.apiurl+'/kecheng/get_kecheng_list_for_kechengbao_id',
          qs.stringify({
              kechengbao_id:this.codeid
          })
          ).then(res =>{
            if(res.data.status=="ok"){
              console.log("课程包列表")
              console.log(res)
              that.data=that.data.concat(res.data.data.kecheng)
              that.price=res.data.data.price
              that.xueshi=res.data.data.xueshi_num
              that.year=res.data.data.year
              that.imgs=res.data.data.img_url
              that.title=res.data.data.title
              console.log(that.imgs)
              for(var i=0;i<res.data.data.kecheng.length;i++){
                  that.idd=res.data.data.kecheng[i].id
              }
            }else if((res.data.status=="error")){
              that.$message.error({message:res.data.errormsg,duration:1600});
            }else if((res.data.status=="relogin")){
              that.removeInfo();
            }
              
          })     
      },
    // gopackdetail (){
    //   this.$router.push({
    //     name:'courseDetails',
    //     params:{
    //         courseId:this.idd
    //     }
    //   })
    // },
    coursedetail(id){
      this.$router.push({
        name:'personcourseDetails',
        params:{
            courseId:id
        }
      });
    },
    //返回按钮
      goback (){
          var that =this
          this.$axios.post(this.apiurl+'/user/logout',
           qs.stringify({
             uid:this.uid,
             token:this.token
           })
          ).then(res =>{
            that.tuichusucess();
          })
      },
    //返回个人中心
    personal(){
        this.$router.push({path:'/my'});
    },
    tuichusucess(){
      let that =this ;
      that.$message.success({message:"退出成功",duration:1600});
      localStorage.removeItem("uid");
      localStorage.removeItem("token");
      localStorage.removeItem("sex");
      localStorage.removeItem("name");
      localStorage.removeItem("mobile");
      localStorage.removeItem("id_card");
      localStorage.setItem("types",'rate');
      setTimeout(() => {
        that.$router.push({ path: '/index' });
      }, 1600);
    },
    removeInfo(){
      let that =this ;
      that.$message.error({message:"重新登录",duration:1600});
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

  },
};
</script>

<style lang="less" scoped>
.my {
  width: 100%;
  height: 100%;
  background-color: #f4f4f4;
  cursor: default;
  .w {
    width: 1200px;
    margin: 0 auto;
  }
  
  .title {
    width: 100%;
    height: 72px;
    line-height: 72px;
    background-color: #0169cc;
    color: #fff;
    font-size: 28px;
    letter-spacing: 2px;
    .fr {
      font-size: 16px;
      .spn1 {
        margin-right: 45px;
      }
      span{cursor: pointer;}
    }
  }
  
  }
.packagedetail {
  width: 100%;
  .title {
    width: 100%;
    height: 72px;
    line-height: 72px;
    background-color: #0169cc;
    color: #fff;
    font-size: 28px;
    letter-spacing: 2px;
    .fr {
      font-size: 16px;
      .spn1 {
        margin-right: 45px;
      }
    }
  }
  .coursebg {
    margin: 15px auto 0;
    background-color: #fff;
    width: 1200px;
  }
  .wrap {
    width: 1200px;
    margin: 0 auto;
    overflow: hidden;
  }
  .coursetopbox {
    padding: 20px 0;
  }
  .coursetopbox .left {
    position: relative;
    height: 320px;
    display: inline-block;
    vertical-align: top;
  }
  .coursetopbox .left img {
    width: 580px;
    height: 320px;
    display: block;
    margin-left: 15px;
  }
  .coursetopbox .right {
    position: relative;
    display: inline-block;
    vertical-align: top;
    margin-left: 50px;
    height: 320px;
  }
  .coursetopbox h3 {
    margin-top: 40px;
    margin-bottom: 10px;
    color: #333;
    font-size: 24px;
    line-height: 40px;
  }
  .coursetopbox .info {
    margin-bottom: 20px;
    color: #666;
    line-height: 20px;
  }
  .coursetopitem {
    color: #333;
    font-size: 14px;
    line-height: 30px;
  }
  .coursetopitem {
    color: #333;
    font-size: 14px;
    line-height: 30px;
  }
  .coursetopitem {
    color: #333;
    font-size: 14px;
    line-height: 50px;
  }
  .coursetopitem .fl {
    line-height: 40px;
    font-size: 14px;
  }
  .coursetopitem .new_price {
    float: left;
    margin-right: 30px;
    color: #fb7d30;
  }
  .coursetopitem .new_price span {
    font-size: 24px;
    line-height: 30px;
  }
  .coursetopitem.btn {
    margin-top: 85px;
    float: left;
  }
  .coursetopitem .buy {
    float: left;
    margin-right: 25px;
    width: 204px;
    height: 47px;
    background-color: #e82f24;
    color: #fff;
    text-align: center;
    font-size: 22px;
    line-height: 47px;
  }
  .coursemain {
    // width: 880px;
    background: #fff; // float: left;
  }
  .coursemain-hd {
    font-size: 18px;
  }
  .coursemain-hd ul {
    overflow: hidden;
    margin: 0 15px;
    border-bottom: 1px solid #edf2f2;
    height: 60px;
    line-height: 58px;
    background-color: white;
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }
  .coursemain-hd li {
    margin-right: 60px;
    height: 100%;
  }
  .coursemain-hd li a {
    display: block;
    height: 58px;
    color: #666;
    font-size: 18px;
    line-height: 58px;
  }
  .coursemain-hd li .icon-01 {
    background-position: -115px -416px;
  }
  .coursemain-hd li.on a {
    border-bottom: solid 2px #e82f24;
    color: #e82f24;
  }
  .coursemain-bd {
    padding: 20px 15px;
  }
  .coursemain-bd .coursemain_con {
    font-size: 14px;
    line-height: 20px;
    color: #666;
    text-indent: 28px;
  }
  .coursemainlist li {
    // position: relative;
    padding: 25px 30px 25px 170px; // height: 78px;
    border-bottom: solid 1px #e2e2e2; //  border: 1px solid red;
    cursor: pointer;
  }
  .coursemainlist li > a {
    display: block;
    color: #000;
  }
  .coursemainlist li h3 {
    margin-bottom: 10px;
  }
  .coursemainlist li h3 a {
    font-size: 18px;
    display: block;
    color: #333;
  }
  .coursemainlist img {
    position: absolute;
    margin-left: -170px;
    width: 140px;
    height: 78px;
  }
  .coursemainlist p {
    color: #999;
    font-size: 13px;
    margin-bottom: 5px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .coursemainlist .p_price {
    font-size: 13px;
    color: #333;
  }
  .coursemainlist .info {
    font-size: 13px;
    color: #999;
  }
}
</style>