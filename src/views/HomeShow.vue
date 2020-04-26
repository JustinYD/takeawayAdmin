<template>
  <div>
    <el-row justify="space-between" type="flex">
      <el-col :span="5">
        <div class="userTotal totalBox">
          <h3>总用户数</h3>
          <h1>{{userTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="orderTotal totalBox">
          <h3>总订单数</h3>
          <h1>{{orderTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="sendTotal totalBox">
          <h3>配送中</h3>
          <h1>{{sendTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="finishTotal totalBox">
          <h3>已完成订单</h3>
          <h1>{{finishTotal}}</h1>
        </div>
      </el-col>
    </el-row>
    <el-row :gutter="35">
      <el-col :span="6">
        <div id="main" class="echartsBox"></div>
      </el-col>
      <el-col :span="14">
        <div class="settingBox">
          <div>
            短信发送阈值设置
            <el-input-number v-model="min" :min="120" :max="5000" :step="50"></el-input-number>米
            <el-button @click="changeMin" class="changebtn">修改</el-button>
          </div>
          <!-- <div>
            
            添加通知<el-input placeholder="请输入内容" v-model="notice" clearable></el-input>
          </div>-->
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<script>
import echartsLiquidfill from "echarts-liquidfill";
export default {
  data() {
    return {
      min: 3000,
      test: "",
      notice: "",
      userTotal: 0,
      orderTotal: 0,
      sendTotal: 0,
      finishTotal: 0
    };
  },
  methods: {
    changeMin() {
      let min = this.min;
      let that=this
      that.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_changemin",
          { min: min },
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.$message.success('修改成功！');
              that.getmin()
            }
          },
          function(res) {
            console.log(res.status);
          }
        );
    },
    //获取用户列表
    getuser() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_userlist").then(
        function(res) {
          if (res.body != "fail") {
            that.userTotal = res.body.length;
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取订单列表
    getorder() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_orderlist").then(
        function(res) {
          if (res.body != "fail") {
            that.orderTotal = res.body.length;
            that.drawTest();
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取成功订单列表
    getsuccess() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_successlist").then(
        function(res) {
          if (res.body != "fail") {
            that.finishTotal = res.body.length;
            that.drawTest();
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取配送中订单列表
    getsend() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_sendlist").then(
        function(res) {
          if (res.body != "fail") {
            that.sendTotal = res.body.length;
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    getmin() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_getmin").then(
        function(res) {
          if (res.body != "fail") {
            that.min = res.data[0][1];
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    drawTest() {
      var total = this.orderTotal;
      var finish = this.finishTotal;
      var value = finish / total;
      var data = [];
      data.push(value);
      data.push(value);
      data.push(value);
      data.push(value);
      data.push(value);
      data.push(value);
      //var echarts = require("echarts");

      var myChart = this.$echarts.init(document.getElementById("main"));
      // 绘制图表
      myChart.setOption({
        backgroundColor: "#fff",
        title: {
          text: "配送完成率",
          textStyle: {
            fontWeight: "normal",
            fontSize: 25,
            color: "rgb(97, 142, 205)"
          }
        },
        series: [
          {
            type: "liquidFill",
            radius: "80%",
            data: data,
            backgroundStyle: {
              borderWidth: 5,
              borderColor: "rgb(255,0,255,0.9)",
              color: "rgb(255,0,255,0.01)"
            },
            label: {
              normal: {
                formatter: (value * 100).toFixed(2) + "%",
                textStyle: {
                  fontSize: 50
                }
              }
            }
          }
        ]
      });
    }
  },
  created() {
    this.getuser();
    this.getorder();
    this.getsuccess();
    this.getsend();
    this.getmin();
  },
  mounted() {
    this.drawTest();
  }
};
</script>
<style>
.totalBox {
  border-radius: 5px;
  padding: 10px;
  color: white;
  cursor: pointer;
}
.totalBox h1 {
  font-size: 25px;
}
.totalBox h3 {
  font-size: 15px;
}
.userTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(109, 205, 243),
    rgb(117, 22, 224)
  );
}
.userTotal:hover {
  box-shadow: 0 5px 12px 0px rgb(109, 205, 243, 0.8);
}
.orderTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(255, 0, 234)
  );
}
.orderTotal:hover {
  box-shadow: 0 5px 12px 0px rgb(255, 0, 234, 0.5);
}
.sendTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 51, 211),
    rgb(183, 0, 255)
  );
}
.sendTotal:hover {
  box-shadow: 0 5px 12px 0px rgb(23, 51, 211, 0.5);
}
.finishTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(0, 162, 255)
  );
}
.finishTotal:hover {
  box-shadow: 0 5px 12px 0px rgb(23, 211, 195, 0.5);
}
.echartsBox {
  width: 300px;
  height: 300px;
  margin-top: 20%;
}
.settingBox {
  border-radius: 10px;
  padding: 20px;
  width: 100%;
  height: 100%;
  margin-top: 7%;
  box-shadow: 0 5px 12px 0px rgb(199, 206, 204);
}
.changebtn {
  color: white;
  background-image: linear-gradient(
    to bottom right,
    rgb(68, 180, 232),
    rgb(170, 0, 255)
  );
}
</style>\