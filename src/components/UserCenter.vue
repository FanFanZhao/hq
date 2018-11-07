<template>
  <div id="user-center">
    <!-- <div class="title">个人中心</div> -->
    <div class="content flex">
      <ul class="content-l">
        <li>
          <img src="../assets/images/logo.png" alt="">
          <router-link to="/userCenter">账户设置</router-link>
        </li>
        <li>
          <img src="../assets/images/logo.png" alt="">
          <router-link to="/">账户设置</router-link>
        </li>
        <li>
          <img src="../assets/images/logo.png" alt="">
          <router-link to="/dealCenter">账户设置</router-link>
        </li>
      </ul>
      <div class="content-r">
        <router-view></router-view>
      </div>

    </div>
  </div>
</template>

<script>
export default {};
</script>

<style lang='scss'>
#user-center {
  margin: 0 auto;
  width: 1200px;
  .router-link-active{
    color: #d45858;
  }
  >.content{
    padding-top: 50px;
    >ul,>div{
      padding: 0 30px;
      background: #f8f8f8;
    }
    >.content-l{
      margin-right: 30px;
      line-height: 40px;
      text-align: center;
      img{
        width: 20px;
        height: 20px;
        vertical-align: sub;
      }
    }
  }
}
</style>
