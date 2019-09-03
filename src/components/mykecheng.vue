<template>
  <div class="mykecheng fl">
    <div class="title">
      <span>我的课程</span>
    </div>
    <div class="content">
        <ul class="clearfix" v-show="datalist.length">
            <li class="fl" v-for="item in datalist" :key="item.id" @click="todetail(item.id)">
                <p><img :src="item.img_url" alt=""></p>
                <p class="txt">{{item.title}}(<span>{{item.xueshi_num}}</span>课时)</p>
                <!-- <p class="tit">{{item.title}}</p>   -->
                <p style="padding:10px 0;box-sizing:border-box; "><el-progress :percentage='parseFloat(item.progress)' :id="item.id"></el-progress></p>
                <p style="color:red;"><span>主讲:&nbsp;</span>{{item.jiangshi.name}}</p>         
            </li>
           
        </ul>
          <div class="blocks" style="text-align:right;margin-right:20px;margin-top:20px;">
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
        <div class="nodata" v-show="!datalist.length" >
        </div>
    
    </div>
  </div>
</template>

<script>
import qs from 'qs'
import Vue from 'vue'
export default {
  name:'mykecheng',
   data() {
      return {
        used: 0,
        uid:'',
        token:'',
        year:[],
        datalist:[],
        idd:'',
        apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
         fenye:true,
       count:0,
        page:1,
        num:6,
        pageNo:1,
       
      }
    },
    created (){
        this.uid=localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
        var date=new Date;
        this.year=date.getFullYear()
        
        if(this.token){
        this.checkkecheng()
    }else{
        this.removeInfo()
    }
        
    },
     watch: {
		token: {
			handler: function(val) {
				if (val) {
          //  this.checkkecheng()
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
           localStorage.setItem('pagenum',this.page)
           this.checkkecheng ()
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
      //我的课程
      checkkecheng(){
         var that=this
         this.$axios.post(this.apiurl+'/kecheng/get_list_for_buy',
          qs.stringify({
             uid:this.uid,
             token:this.token,
             year:this.year,
             page:this.page,
             num:this.num
          })
         ).then(res =>{
            console.log(res)
            if(res.data.status=="ok"){
              that.datalist=[]
              that.datalist=that.datalist.concat(res.data.data.data)
              that.count=Number(res.data.data.count)
              console.log( that.datalist)
              for(var i=0;i<that.datalist.length;i++){
                Vue.set(that.datalist[i],"progress","0")
                //  获取课程id
                  this.getprogress(that.datalist[i].id)
              }
            }else if((res.data.status=="error")){
              this.$message.error({message:res.data.errormsg,duration:1600});
            }else if((res.data.status=="relogin")){
              that.removeInfo();
            }
           
         }) 
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
    //获取课程进度
    getprogress (id){
        var that=this
        console.log(id)
        that.$axios.post(this.apiurl+'/kecheng/get_kecheng_jindu',
            qs.stringify({
              kecheng_id:id,
              uid:that.uid,
              token:that.token
            })
        ).then(res =>{
          if(res.data.status=="ok"){
            // console.log(obj)
            for(var i=0;i<that.datalist.length;i++){
                   if(res.data.data['kecheng_id']==that.datalist[i].id){
                      Vue.set(that.datalist[i],"progress",res.data.data.progress)
                      break
                   }
              
            }
          }else if((res.data.status=="error")){
            that.used=0;
          }else if((res.data.status=="relogin")){
             that.removeInfo();
          }
        })
    },
   
    }

};
</script>

<style scoped lang="less">
.mykecheng{
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
      ul{
          li{
              // border:1px solid red;
               width: 260px;
                height: 285px;
                margin-left: 32px;
                margin-top: 30px;
                border: 1px solid #dfe4ed;
                border-radius: 5px;
                padding: 10px;
                background-color: #fff;
                P{
                  img{
                    width: 100%;
                    // height: 189px;
                    border-radius: 5px;
                    overflow: hidden;
                  }
                  &.txt{
                    width: 100%;
                    height: 34px;
                    padding-left: 8px;
                    background-color: rgba(0,0,0,.4);
                    font-size: 14px;
                    color: #fff;
                    line-height: 34px;
                    margin-top:15px;
                    border-radius: 0 0 5px 5px;
                  }
                  &.tit{
                    height: 22px;
                    font-size: 16px;
                    color: #717171;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    white-space: nowrap;
                    margin-top: 12px;
                  }
                  &.jindu{
                    width: 100%;
                    height: 12px;
                    display: flex;
                    justify-content: space-between;
                    margin-top: 16px;
                    span{
                      &.fr{
                        margin-top:-12px;
                      }
                    }
                  }
                }
          }
          li:hover{
           box-shadow: 0 0 10px #c1c1c1; 
           border:1px solid #c1c1c1; 
          }
      }
      .nodata{
                width: 212px;
                height: 240px;
                margin: 80px auto;
                background-image: url('../assets/nodata.png');
            }
  }

}
</style>
<style lang="less">
    .mykecheng{
        .block{
          width: 70%;
          height: 10px;
        }
        .el-slider{
            height: 10px;
        }
        .el-slider__runway{
          margin:-5px 0 0 40px;
        }
    }
</style>
