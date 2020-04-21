<template>
    <div>
        <el-menu
        default-active="1"
        router="router"
        class="el-menu-vertical-demo">
            <el-menu-item index="1" route="homeshow">
                <i class="el-icon-s-home"></i>
                <span slot="title">首页</span>
            </el-menu-item>
            <el-menu-item index="2" route="usermanager">
                <i class="el-icon-user-solid"></i>
                <span slot="title">用户订单</span>
            </el-menu-item>
            
        </el-menu>
    </div>
</template>
<script>
export default {
    name:'TapBar',
   data() {
      return {
      }
   },
   methods: {
      handleOpen(key, keyPath) {
        console.log(key, keyPath);
      },
      handleClose(key, keyPath) {
        console.log(key, keyPath);
      }
    }
}
</script>
<style>
/* pageFullScreen在index.css */
.test{
    color: rgb(14, 253, 46);
}
</style>
