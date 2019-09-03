<template>
  <div class="allCourses fl">
    <div class="title">
      <span>全部课程</span>
    </div>
    <div class="content">
      <ul class="clearfix">
        <li class="fl" v-for="item in allcourse" :key="item.id" @click="todetail(item.id)">
          <p class="imm">
            <img :src="item.img_url" alt />
          </p>
          <p class="txt">
            {{item.title}}(
            <span>{{item.xueshi_num}}</span>学时)
          </p>
          <p class="tit">{{item.title}}</p>
          <p class="jindu"><span>讲师:&nbsp;</span>{{item.jiangshi.name}}</p>
        </li>
      </ul>
      <div class="block" v-show="fenye" style="text-align:right;margin-top:20px;">
          <el-pagination
              @current-change="handleCurrentChange"
              :current-page.sync="pageNo"
              :page-size="6"
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
import qs from "qs";
export default {
  data() {
    return {
      pageNo:1,
      count:0,
      page:1,
      year: "",
      uid: "",
      token: "",
      fenye:true,
      num: 6,
      allcourse: [],
      pagesize: 1,
      yenum: "",
      allxueshi: "",
      kechengxueshi: "",
      used:0,
      id:[],
      idd:'',
      apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
    };
  },
  created() {
    var that=this
    this.uid = localStorage.getItem("uid");
    this.token = localStorage.getItem("token");
    var date = new Date();
    this.year = date.getFullYear();
    console.log(this.token)
   
    if(this.token){
         this.getallcourse();
    }else{
        this.removeInfo()
    }
  },
 watch: {
		token: {
			handler: function(val) {
				if (val) {
            // this.getallcourse(); 
				}else{
           this.removeInfo()
        }
			}
		},deep:true
	},
  methods: {
    //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           this.getallcourse ()
          },
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
      }, 500);
    },
    //获取全部课程
    getallcourse() {
      var that = this;
      var datalist = {
        num: this.num,
        year: this.year,
        page: this.page,
        year:this.year,

      };
      this.$axios
        .post(
          this.apiurl+'/kecheng/get_kecheng_list',
          qs.stringify(datalist)
        )
        .then(res => {
          if(res.data.status=="ok"){
            console.log("获取全部课程");
            console.log(res);
            that.allcourse=[];
            that.allcourse = that.allcourse.concat(res.data.data.data);
            that.count = parseInt(res.data.data.count);
            that.pagesize = res.data.data.pagesize;
            for(var i=0 ;i<res.data.data.data.length;i++){
                that.id=that.id.concat(res.data.data.data[i].id)
            }
            for(var i=0;i<that.id.length;i++){
              that.idd=that.id[i]
              console.log(this.idd)
            }
          }else if((res.data.status=="error")){
            this.$message.error({message:res.data.errormsg,duration:1600});
          }else if((res.data.status=="relogin")){
            this.$message.error({message:"重新登录",duration:1600});
            that.removeInfo();
          }
        });
    },
    //获取课程进度
    getprogress (){
        var that=this
        that.$axios.post(this.apiurl+'/kecheng/get_kecheng_jindu',
            qs.stringify({
              kecheng_id:that.id,
              uid:that.uid,
              token:that.token
            })
        ).then(res =>{
          if(res.data.status=="ok"){
            console.log("获取进度")
            console.log(res)
            console.log(res.data.status)
            if(res.data.status=='error'){
              that.used=0
            }else{
               if(res.data.status=="ok"){
                  that.used=res.data.progress
               }
            }
          }else if((res.data.status=="error")){
            this.$message.error({message:res.data.errormsg,duration:1600});
          }else if((res.data.status=="relogin")){
            this.$message.error({message:"重新登录",duration:1600});
            that.removeInfo();
          } 
        });
    },
    //到课程详情页
    todetail (val){
      console.log(val)
        this.$router.push({
          name:'personcourseDetails',
          params:{
            courseId:val
          }
        })
      
    },
    changePage (){
      // this.pageNo=page
      console.log(this.pageNo)
       this.getallcourse()
    }
  }
};
</script>

<style scoped lang="less">
.allCourses {
  width: 948px;
  .title {
    width: 100%;
    height: 55px;
    background-color: #fafafa;
    // margin-bottom: 20px;
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
    ul {
      li {
        // border:1px solid red;
        width: 260px;
        // height: 285px;
        margin-left: 32px;
        margin-top: 15px;
        border: 1px solid #dfe4ed;
        border-radius: 5px;
        padding: 10px;
        background-color: #fff;
        cursor: pointer;
        p {
          &.imm {
            height: 130px;
          }
          img {
            width: 100%;
            height: 100%;
            border-radius: 5px;
            overflow: hidden;
          }

          &.txt {
            width: 100%;
            height: 34px;
            padding-left: 8px;
            background-color: rgba(0, 0, 0, 0.4);
            font-size: 14px;
            color: #fff;
            line-height: 34px;
            margin-top: 15px;
            border-radius: 0 0 5px 5px;
          }
          &.tit {
            height: 22px;
            font-size: 16px;
            color: #717171;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            margin-top: 12px;
          }
          &.jindu {
            color:red;
           
          }
        }
      }
      li:hover {
        box-shadow: 0 0 10px #c1c1c1;
        border: 1px solid #c1c1c1;
      }
    }
    .block{
      text-align:right;
      margin-right: 20px;
    }
  }
}
</style>