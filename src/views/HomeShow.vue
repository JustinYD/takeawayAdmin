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
            <el-button @click="addNotice" class="addbtn">添加通知</el-button>
          </div>
          <div>
            <el-divider>通知列表</el-divider>
            <el-table :data="notice" height="270" style="width: 100%">
              <el-table-column fixed prop="content" label="内容" sortable></el-table-column>
              <el-table-column fixed prop="status" label="状态"  sortable></el-table-column>
              <el-table-column fixed prop="time" label="创建时间" sortable></el-table-column>
              <el-table-column fixed="right" label="操作">
                <template slot-scope="scope" >
                  <el-popconfirm size="small" v-if="scope.row.status=='正常'" title="确定下架该通知？" @onConfirm="deletenotice">
                    <el-button slot="reference" type="danger" @click="downnotice(scope.row)" size="small">下架</el-button>
                  </el-popconfirm>
                  <el-popconfirm size="small" v-else title="确定发布该通知？" @onConfirm="updatenotice">
                    <el-button slot="reference" type="primary" @click="upnotice(scope.row)" size="small">发布</el-button>
                  </el-popconfirm>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </el-col>
      <el-dialog title="添加通知" :visible.sync="addflag">
        <el-form :model="add">
          <el-form-item label="内容" :label-width="formLabelWidth">
            <el-input v-model="add.content" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addflag = false">取 消</el-button>
          <el-button type="primary" @click="addNoticeBtn">确 定</el-button>
        </div>
      </el-dialog>
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
      notice: [],
      userTotal: 0,
      orderTotal: 0,
      sendTotal: 0,
      finishTotal: 0,
      addflag: false,
      delete:{},
      add: {
        content: "",
        time: ""
      },
      formLabelWidth: "60px"
    };
  },
  methods: {
    downnotice(row){
      this.delete=row
    },
    upnotice(row){
      this.delete=row
    },
    updatenotice(){
      var that = this;
      let row=that.delete
      this.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_updatenotice",
          { time: row.time },
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.getAllNotice()
              that.$message.success('发布成功！');
            } else {
              that.$message.error('已经发布！');
            }
          },
          function(res) {
            that.$message.success(res);
          }
        );
    },
    deletenotice(){
      var that = this;
      let row=that.delete
      this.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_deletenotice",
          { time: row.time },
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.getAllNotice()
              that.$message.success('下架成功！');
            } else {
              that.$message.error('已经下架！');
            }
          },
          function(res) {
            that.$message.success(res);
          }
        );
    },
    addNoticeBtn() {
      Date.prototype.Format = function(fmt) {
        var o = {
          "M+": this.getMonth() + 1, // 月份
          "d+": this.getDate(), // 日
          "h+": this.getHours(), // 小时
          "m+": this.getMinutes(), // 分
          "s+": this.getSeconds(), // 秒
          "q+": Math.floor((this.getMonth() + 3) / 3), // 季度
          S: this.getMilliseconds() // 毫秒
        };
        if (/(y+)/.test(fmt))
          fmt = fmt.replace(
            RegExp.$1,
            (this.getFullYear() + "").substr(4 - RegExp.$1.length)
          );
        for (var k in o)
          if (new RegExp("(" + k + ")").test(fmt))
            fmt = fmt.replace(
              RegExp.$1,
              RegExp.$1.length == 1
                ? o[k]
                : ("00" + o[k]).substr(("" + o[k]).length)
            );
        return fmt;
      };
      this.add.time = new Date().Format("yyyy-MM-dd hh:mm:ss");
      let that = this;
      this.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_addnotice",
          that.add,
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.$message.success("添加成功！");
              that.getAllNotice();
              that.addflag=false;
            }
          },
          function(res) {
            console.log(res.status);
          }
        );
    },
    addNotice() {
      this.addflag = true;
    },
    
    changeMin() {
      let min = this.min;
      let that = this;
      that.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_changemin",
          { min: min },
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.$message.success("修改成功！");
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
    getAllNotice() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_getallnotice").then(
        function(res) {
          if (res.body != "fail") {
            that.notice = [];
            for (let i = 0; i < res.data.length; i++) {
              let flag = "";
              if (res.data[i][2] == "1") {
                flag = "正常";
              } else {
                flag = "已下架";
              }
              that.notice.push({
                content: res.data[i][0],
                time: res.data[i][1],
                status: flag
              });
            }
          }
        },
        function(res) {
          that.$message.success(res.status);
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
    this.getAllNotice();
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
  box-shadow: 0 5px 12px 0px rgba(162, 163, 164, 0.8);
  color: rgba(97, 97, 97, 0.81);
  border: rgb(198, 193, 193) solid 1px;
}
.userTotal:hover {
  background-image: linear-gradient(
    to bottom right,
    rgb(109, 205, 243),
    rgb(117, 22, 224)
  );
  color: white;
  box-shadow: 0 5px 12px 0px rgba(255, 255, 255);
}
.orderTotal {
  box-shadow: 0 5px 12px 0px rgba(162, 163, 164, 0.8);
  color: rgba(97, 97, 97, 0.81);
  border: rgb(198, 193, 193) solid 1px;
  
}
.orderTotal:hover {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(255, 0, 234)
  );
  box-shadow: 0 5px 12px 0px rgb(255, 255, 255);
  color: white;
}
.sendTotal {
  box-shadow: 0 5px 12px 0px rgba(162, 163, 164, 0.8);
  color: rgba(97, 97, 97, 0.81);
  border: rgb(198, 193, 193) solid 1px;
}
.sendTotal:hover {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 51, 211),
    rgb(183, 0, 255)
  );
  box-shadow: 0 5px 12px 0px rgba(254, 254, 254);
  color: white;
}
.finishTotal {
  box-shadow: 0 5px 12px 0px rgba(162, 163, 164, 0.8);
  color: rgba(97, 97, 97, 0.81);
  border: rgb(198, 193, 193) solid 1px;
  
}
.addbtn {
  color: white;
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(0, 162, 255)
  );
}
.finishTotal:hover {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(0, 162, 255)
  );
  box-shadow: 0 5px 12px 0px rgb(252, 252, 252);
  color: white;
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