<template>
  <div class="archives fl">
    <div class="title">
      <span>档案记录</span>
    </div>
    <div class="content">
      <div class="top">
        <ul class="clearfix">
          <li class="l1">年度</li>
          <li>姓名</li>
          <li>身份证号</li>
          <li>学时</li>
          <li class="l6">操作</li>
        </ul>
      </div>
      <div class="topcon" v-show="!nodata">
        <ul v-for="item in list" :key="item.id">
          <li class="l1">{{item.year}}</li>
          <li>{{item.name}}</li>
          <li>{{item.id_card}}</li>
          <li>{{parseInt(item.get_gongxuke_xueshi_num)+parseInt(item.get_zhuanyeke_xueshi_num)}}</li>
          <li class="l6" @click="downloadCertificate(item.id)">下载证书</li>
        </ul>
      </div>
      <div class="nodata" v-show="nodata"></div>
      <div class="my-certificate" ref="certificate">
        <img src="/static/images/personal/certificate.png" class="certificateimg" />
        <div class="certificate-box">
          <p class="certificate-title">
            河南省专业技术人员继续教育网络学习
            <br />合格证明
          </p>
          <div class="info1 info">
            <div class="name-box">
              姓名：
              <span class="name">{{name}}</span>
            </div>
            <div class="idcard-box">
              身份证号：
              <span class="idcard">{{id_card}}</span>
            </div>
          </div>
          <div class="info2 info">
            <div class="sex-box">
              性别：
              <span class="sex">{{sex=='1'?"男":'女'}}</span>
            </div>
            <div class="year-box">
              培训年度：
              <span class="year">{{year}}年</span>
            </div>
          </div>
          <div class="xueshi">
            该学员参加年度专业技术人员继续教育专业科目的网络学习，通过考核。
            <br />累计完成
            <span class="alltime" v-html="xueshinum"></span>学时。
          </div>
          <div class="tczm">特此证明。</div>
          <div class="date" v-html="riqi"></div>
          <div class="blackline"></div>
        </div>
        <img src="/static/images/personal/seal.png" class="seal" />
      </div>
    </div>
  </div>
</template>

<script>
import qs from "qs";
import html2canvas from "html2canvas";
export default {
  data() {
    return {
      nodata: false,
      uid: "",
      token: "",
      year: "",
      list: "", //返回来的数据
      name: "",
      id_card: "",
      riqi: "",
      apiurl: "http://jixujiaoyu_api.songlongfei.club:1012",
      sex: "",
      xueshinum: ""
    };
  },
  created() {
    this.uid = localStorage.getItem("uid");
    this.token = localStorage.getItem("token");
    this.sex = localStorage.getItem("sex");

    if (this.token) {
      this.dangan();
    } else {
      this.removeInfo();
    }
  },
  watch: {
    token: {
      handler: function(val) {
        if (val) {
          // this.dangan();
        } else {
          this.removeInfo();
        }
      }
    },
    deep: true
  },
  methods: {
    removeInfo() {
      var that=this
      localStorage.removeItem("uid");
      localStorage.removeItem("token");
      localStorage.removeItem("sex");
      localStorage.removeItem("name");
      localStorage.removeItem("mobile");
      localStorage.removeItem("id_card");
      localStorage.setItem("types", "rate");
      setTimeout(() => {
        that.$router.push({ path: "/login" });
      }, 1600);
    },
    dangan() {
      //获取课程包信息
      var that = this;
      var data = {
        year: this.year,
        uid: this.uid,
        token: this.token
      };
      this.$axios({
        method: "post",
        url: this.apiurl + "/dangan/get_user_year_xueshi",
        data: qs.stringify(data)
      }).then(res => {
        if (res.data.status == "ok") {
          console.log(res);
          console.log("档案记录");
          that.list = res.data.data;
          console.log(that.list);
          if (res.data.data.length) {
            that.nodata = false;
          } else {
            that.nodata = true;
          }
        } else if (res.data.status == "error") {
          this.$message.error({ message: res.data.errormsg, duration: 1600 });
        } else if (res.data.status == "relogin") {
          this.$message.error({ message: "重新登录", duration: 1600 });
          that.removeInfo();
        }
      });
    },
    downloadCertificate: function(num) {
      let that = this;
      for (var i = 0; i < that.list.length; i++) {
        if (that.list[i]["id"] == num) {
          that.name = that.list[i]["name"];
          that.year = that.list[i]["year"];
          that.id_card = that.list[i]["id_card"];

          var date = new Date();
          this.year = date.getFullYear();
          var month = date.getMonth() + 1;
          var strDate = date.getDate();
          if (month >= 1 && month <= 9) {
            month = "0" + month;
          }
          if (strDate >= 0 && strDate <= 9) {
            strDate = "0" + strDate;
          }
          var currentdate =
            that.year +
            '<span style="margin: 0 7px;">年</span>' +
            month +
            '<span style="margin: 0 7px">月</span>' +
            strDate +
            '<span style="margin-left:7px;">日</span>';
          this.riqi = currentdate;

          that.xueshinum =
            parseFloat(that.list[i]["get_gongxuke_xueshi_num"]) +
            parseFloat(that.list[i]["get_zhuanyeke_xueshi_num"]);
          console.log(that.xueshinum);
          if (that.list[i]["enabled_download"] == 1) {
            setTimeout(() => {
              html2canvas(this.$refs.certificate).then(function(canvas) {
                var base64 = canvas.toDataURL("image/jpeg", 1);
                var a = document.createElement("a");
                a.href = base64;
                a.download = "证书下载";
                a.click();
              });
            }, 500);
            let isdown = { id: num, uid: this.uid, token: this.token };
            this.$axios({
              method: "post",
              url: this.apiurl + "/dangan/download_notify",
              data: qs.stringify(isdown)
            }).then(function(response) {
              if (response.data.status == "ok") {
                console.log("down");
                console.log(response.data);
              } else if (response.data.status == "error") {
                that.$message.error({
                  message: response.data.errormsg,
                  duration: 1600
                });
              } else if (response.data.status == "relogin") {
                that.removeInfo();
              }
            });
          } else {
            this.$message.error({
              message: "您的学时还未达到下载证书的标准，继续努力哦",
              duration: 3000
            });
          }
        }
      }
    }
  }
};
</script>

<style lang="less" scoped>
.archives {
  width: 948px;
  // border:1px solid red;
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
    .top {
      ul {
        background-color: #138bef;
        width: 100%;
        padding: 0 20px;
        box-sizing: border-box;
        height: 35px;
        display: flex;
        justify-content: space-between;

        li {
          // width: 910px;
          width: 20%;
          height: 35px;
          line-height: 35px;
          font-size: 14px;
          color: #fff;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          text-align: center;
          cursor: pointer;
          &.l1 {
            text-align: left;
          }
          &.l6 {
            text-align: right;
          }
        }
      }
    }
    .topcon {
      ul {
        width: 100%;
        padding: 0 20px;
        box-sizing: border-box;
        height: 35px;
        display: flex;
        justify-content: space-between;

        li {
          // width: 910px;
          width: 20%;
          height: 35px;
          line-height: 35px;
          font-size: 14px;
          color: #868686;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          text-align: center;
          cursor: pointer;
          &.l1 {
            text-align: left;
          }
          &.l6 {
            text-align: right;
            color: #0c69f5;
          }
        }
      }
    }
    .nodata {
      width: 212px;
      height: 240px;
      margin: 80px auto;
      background-image: url("../assets/nodata.png");
    }
  }
  .my-certificate {
    width: 822px;
    height: 590px;
    position: absolute;
    left: -850px;
    top: -600px;
  }
  .certificate-box {
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 1;
  }
  .certificate-box .certificate-title {
    width: 560px;
    line-height: 42px;
    margin: 100px auto 0;
    font-size: 32px;
    text-align: center;
    color: #48538d;
  }
  .certificate-box .info {
    width: 512px;
    display: flex;
  }
  .certificate-box .info1 {
    margin: 60px auto 0;
  }
  .certificate-box .info2 {
    margin: 25px auto 0;
  }
  .certificate-box div {
    font-size: 18px;
    font-family: "KaiTi";
  }
  .certificate-box div span {
    font-size: 18px;
    font-family: "Microsoft YaHei";
  }
  .certificate-box .name-box {
    width: 220px;
  }
  .certificate-box .sex-box {
    width: 220px;
  }
  .certificate-box .xueshi {
    width: 648px;
    margin: 20px auto 0;
    text-indent: 68px;
    font-size: 18px;
    line-height: 30px;
  }
  .certificate-box .alltime {
    width: 60px;
    height: 30px;
    line-height: 30px;
    font-size: 18px;
    display: inline-block;
    text-indent: 0;
    text-align: center;
  }
  .certificate-box .blackline {
    width: 60px;
    height: 1px;
    background-color: #000;
    position: absolute;
    left: 160px;
    top: 394px;
  }

  .certificate-box .tczm {
    width: 648px;
    margin: 20px auto 0;
    text-indent: 72px;
    font-size: 18px;
  }
  .certificate-box .date {
    position: absolute;
    right: 108px;
    bottom: 75px;
    font-size: 18px;
    font-family: "Microsoft YaHei";
  }
  .certificate-box .date span {
    font-size: 18px;
    font-family: "KaiTi";
  }
  .my-certificate .certificateimg {
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
  }
  .my-certificate .seal {
    position: absolute;
    right: 100px;
    bottom: 90px;
    width: 146px;
  }
}
</style>
