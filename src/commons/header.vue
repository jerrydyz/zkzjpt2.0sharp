<template>
  <div class="common-header">
    <div class="top-tips">
        <div class="wrap">
            <div class="welcome">欢迎访问 周口市专业技术人员继续教育平台</div>
            <div style="display: block;" v-html="date" id="date"></div>
        </div>
    </div>
    <div class="header">
        <div class="wrap">
            <img class="web-title" src="/static/images/index/logo.png" alt="">
        </div>
    </div>
    <div class="nav-box">
        <div class="nav-list">
            <router-link v-for="(item,index) in navList" :key="item.index" :to="item.path">{{item.title}}</router-link>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'common-header',
  data () {
    return {
      navList: [
        {title: "首页", path: "/index"},
        {title: "新闻咨询", path: "/news"},
        {title: "政策法规", path: "/laws"},
        {title: "工作动态", path: "/works"},
        {title: "课程中心", path: "/courses"},
        {title: "帮助中心", path: "/helps"},
        {title: "关于我们", path: "/aboutus"}
      ],
      date:'',
    }
  },
  mounted () {
    var week = ["星期日", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六"];
    var localToday = '';
    var d = new Date();
    localToday = d.getFullYear() + "年" + (d.getMonth() + 1) + "月" + d.getDate() + "日";
    this.date =  localToday + "&nbsp;&nbsp;&nbsp;&nbsp;" + week[d.getDay()];
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
    .common-header{width: 100%;}
    .wrap{width: 1200px;margin: 0 auto;overflow: hidden;}
    .top-tips{width: 100%;height: 34px;background-color: #dfe2f8; }
    .top-tips .welcome{font-size: 12px;color: #000;font-weight: bold;line-height: 34px;display: inline-block;}
    .top-tips #date{float: right;font-size: 12px;color: #000;font-weight: bold;line-height: 34px;display: inline-block;}   
    .header{width: 100%;height: 362px;background-image: url(/static/images/index/topbg.png);}
    .header .topbg{display: block;width: 100%;height: 100%;}
    .header .web-title{margin-top: 56px;display: block;}
    
    // .nav li{list-style: none;line-height: 48px;}
    // .nav li a{text-decoration: none;color: #fff;font-size: 20px;}
    .nav-box{padding: 0 30px;box-sizing: border-box;width: 1200px;height: 48px;display: flex;justify-content: space-between;background-color: #952418;margin: -140px auto 0;
        .nav-list{width: 1200px;height: 100%;margin: 0 auto;display: flex;align-items: center;justify-content: space-between;
            a{padding-bottom: 5px;color: #fffbfb;font-size: 18px;}
            a:hover{border-bottom: 2px solid #fffbfb;}
            .router-link-active{border-bottom: 2px solid #fffbfb;}
        }
    }
    
</style>
