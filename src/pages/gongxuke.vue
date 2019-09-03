<template>
  <div class="gongxuke">
    <div class="zktop">
            <div class="login-img">周口市专业技术人员公需课学习平台</div>
        <ul>
            <li><router-link to="/index">首页</router-link></li>
        </ul>
    </div>
    <div class="zkbanner1"><img src="/static/images/index/zkbanner1.jpg" alt=""></div>
    <div class="tab-list">
        <div class="ulbox">
            <div class="item-box">
                <div class="item">
                    <div class="zyname">公需课1</div>
                </div>
                <div class="item-copy">
                    <a href="">进入分类</a>
                </div>
            </div>
            <div class="item-box">
                <div class="item">
                    <div class="zyname">公需课2</div>
                </div>
                <div class="item-copy">
                    <a href="">进入分类</a>
                </div>
            </div>
            
        </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'gongxuke',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
    .zktop{width: 1200px;height: 80px;margin: 0 auto;display: flex;justify-content: space-between;overflow: hidden;}
    .zktop .login-img{display: block;margin: 15px;width: 590px;height: 33px;color:#00479d;font-size: 28px;font-weight: bold;letter-spacing: 2px;}
    .zktop ul{width: 174px;height: 100%;list-style-type:none ;}
    .zktop ul li{width: 58px;height: 100%;float: left;}
    .zktop ul li a{text-decoration: none;width: 100%;height: 100%;line-height: 80px;color: #aaa;}
    .zktop ul li a:hover{color: #000;}
    .zkbanner1{width: 100%;height: 480px;border-bottom:3px solid #eaeaea;}
    .zkbanner1 img{width: 100%;height: 100%; background-size: cover;}
    .tab-list{width: 1360px;min-height: 364px;margin: 20px auto;}
    .tab-list .ulbox{width: 1360px;list-style-type:none;display: flex;justify-content: space-around;flex-wrap: wrap;margin-top: 100px;}
    .tab-list .ulbox>div:nth-child(1){background-image: url("/static/images/index/zk-kinds1.png");margin-right: -50px;}
    .tab-list .ulbox>div:nth-child(2){background-image: url("/static/images/index/zk-kinds2.png");}
    .tab-list .ulbox .item-box{width: 566px;height: 197px;overflow: hidden; margin: 5px;display: block;border: 5px solid #eaeaea;}
    .tab-list .ulbox .item-box:hover{cursor: pointer;}
    .tab-list .ulbox .item-box .item{width: 100%;height: 100%;line-height: 197px;color: #000;font-size: 30px;text-align: center;display: block;}
    .tab-list .ulbox .item-box:nth-child(9) .item .zyname{color: #ccc;}
    .tab-list .ulbox .item-box:nth-child(10) .item .zyname{color: #ccc;}
    .tab-list .ulbox .item-box:nth-child(11) .item .zyname{color: #ccc;}
    .tab-list .ulbox .item-box:nth-child(12) .item .zyname{color: #ccc;}
    .tab-list .ulbox .item-box .item .zyname{letter-spacing: 2px;color: #fff;}
    .tab-list .ulbox .item-box:hover .item-copy{transform: translateY(-187px);transition: all .5s;}
    .tab-list .ulbox .item-box .item-copy{width: 100%;height: 100%;line-height: 197px;color: #000;font-size: 18px;text-align: center;display: block;transform: translateY(108px);background-color: #3390de;}
    .tab-list .ulbox .item-box .item-copy a{text-decoration: none;color: #fff;padding: 10px 30px;border: 1px solid #fff;border-radius: 5px;letter-spacing: 2px;}

</style>
