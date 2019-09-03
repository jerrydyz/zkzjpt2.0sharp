<template>
    <div class="examination fl">
        <div class="title">
            <span>参加考试</span>        
        </div>
        <div class="content">
            <div class="top">
                <ul class="clearfix">
                    <li class="fl l1">年度</li>
                    <li class="fl l2">试卷名称</li>
                    <li class="fl l3">总分数</li>
                    <!-- <li class="fl l4">公需课学时</li>
                     <li class="fl l4">专业课学时</li> -->
                      <li class="fl l4">学时</li> 
                      <li class="fl l4">课程题目</li>
                     <li class="fl l5">状态</li>
                     <li class="fl l6">操作</li>
                </ul>
            </div>
             <div class="topcon">
                <ul class="clearfix" v-for="item in data" :key="item.id">
                    <li class="fl l1">{{item.year}}</li>
                    <li class="fl l2">{{item.shijuan_info.title}}</li>
                     <li class="fl l3">{{item.shijuan_info.score}}</li>
                      <!-- <li class="fl l4">{{item.gongxuke_xueshi_num}}</li>
                       <li class="fl l4">{{item.zhuanyeke_xueshi_num}}</li> -->
                        <li class="fl l4">{{(Number(item.gongxuke_xueshi_num)+Number(item.zhuanyeke_xueshi_num)).toFixed(1)}}</li>
                        <li class="fl l7">{{item.kecheng_title}}</li>
                     <!-- <li class="fl l5">{{item.is_pass=="0"?'未通过':"考试通过"}}</li> -->
                    <li class="fl l5 active" v-if="item.is_pass=='0'">{{item.is_pass=="0"?'未通过':"考试通过"}}</li>
                    <li class="fl l5 active1" v-else>{{item.is_pass=="0"?'未通过':"考试通过"}}</li>
                    <li class="fl l6" @click="gostady($event,item.id)">{{item.is_pass==1?'--':item.is_pass==0&&item.enable_kaoshi==0?'继续学习':item.is_pass==0&&item.enable_kaoshi==1?'去考试':'' }}</li>
                </ul>
            </div>
             <!-- <div class="block" style="text-align:right;margin-top:20px;">
                <el-pagination
                    layout="prev, pager, next,jumper"
                    :total="data.length"
                    :page-size="num"    
                    @current-change="current_change" 
                    :current-page.sync="currentPage" 
                    >
                </el-pagination>
                </div> -->
            <div class="nodata" v-show="nodata">

            </div>
             <div class="block" v-show="fenye" style="text-align:right;margin-top:20px;">
                <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="pageNo"
                    :page-size="14"
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
export default {
    data(){
        return{
            nodata:false,
            data:[],
            year:'',
            uid:'',
            token:'',
            page:1,
            currentPage:1,
            apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
            fenye:true,
             page:1,
            num:14,
            count:0,
              pageNo:1,
         
        }
    },
     watch: {
		token: {
			handler: function(val) {
				if (val) {
            // this.getdata ()
				}else{
           this.removeInfo()
        }
			}
		},deep:true
	},
    created (){
        var that=this
        var date=new Date;
        this.year=date.getFullYear()
        this.uid=localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
       
        
    if(this.token){
         this.getdata ()
    }else{
        this.removeInfo()
    }

    },
    methods :{
        //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           localStorage.setItem('pagenum',this.page)
           this.getdata ()
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
       getdata (){
            var that=this
            this.$axios.post(this.apiurl+'/kaoshi/get_shijuan_list',
                qs.stringify({
                    year:this.year,
                    uid:this.uid,
                    token:this.token,
                    page:this.page,
                    num:this.num
                })
            ).then(res =>{
                if(res.data.status=="ok"){
                    console.log("获取考试信息")
                    console.log(res)
                    that.data=[]
                    that.data=that.data.concat(res.data.data.data)
                     that.count=Number(res.data.data.count)
                    console.log(that.data)
                    if(res.data.data.data){
                        that.nodata=false
                    }else{
                        that.nodata=true
                    }
                }else if((res.data.status=="error")){
                    that.$message.error({message:res.data.errormsg,duration:1600});
                }else if((res.data.status=="relogin")){
                    that.removeInfo();
                }
            });
       },
       gostady (e,num){
           var that=this
           console.log(e,num)
           if(e.target.innerHTML=="继续学习"){
               that.$emit('more','allCourses')
           }
           if(e.target.innerHTML=="去考试"){
               that.$router.push({
                        path:'/kaoshi',
                        query:{
                            shijuan_id:num
                        }
                    })
           }
       },
       current_change (currentPage){
           this.currentPage=currentPage
           console.log(currentPage)
       }
    }

}
</script>

<style lang="less" scoped>
    .examination{
        width: 948px;
        // border:1px solid red;
        cursor: default;
        
        .title{
            width: 100%;
            height: 55px;
            background-color: #fafafa;
            margin-bottom: 20px;
            font-size: 18px;
            padding-left: 20px;
            line-height: 55px;
            color: #0c69f5;
            box-sizing: border-box;
            span{
                border-bottom:2px solid #0c69f5;
            }
        }
        .content{
            width: 100%;
            .top{
                 ul{
                  background-color: #138bef;
                  width: 100%;
                  padding: 0 20px;
                    box-sizing: border-box;
                li{
                    // width: 910px;
                    height: 35px;
                    line-height: 35px;
                    font-size:14px;
                    color:#fff;
                    text-align: center;
                    width: 14.2%;
                     cursor: default;
                     &.l1{
                        text-align: left;
                        // width: 15%;
                    }
                    &.l2{   
                        // width: 25%;
                    }
                     &.l3{
                         }
                     &.l4{
                    }
                     &.l6{
                        text-align: right;
                        cursor: pointer;
                    }
                }
            }
            }
             .topcon{
                 ul{
                //   background-color: #138bef;
                  width: 100%;
                  padding: 0 20px;
                    box-sizing: border-box;
                      cursor: pointer;
                li{
                    // width: 910px;
                   height: 35px;
                    line-height: 35px;
                    font-size:14px;
                    color:#868686;
                    text-align: center;
                    width: 14.2%;
                     cursor: default;
                     &.l1{
                        text-align: left;
                        // width: 15%;
                    }
                    &.l2{   
                        // width: 25%;
                    }
                     &.l3{
                         }
                     &.active{
                         color:red;
                    }   
                      &.active1{
                         color:green;
                    } 

                     &.l6{
                         text-align: right;
                    }
                    &.l7{
                        overflow: hidden;
                         text-overflow:ellipsis;
                          white-space: nowrap;
                    }
                }
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
