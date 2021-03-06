<template>
  <div class="header">
    <div class="content">
      <router-link to="/" class="middle-hide">
        <div class="logo">
          <img src="/static/img/logo.png" alt="">
        </div>
      </router-link>
      <div class="nav">
        <ul class="mobile-nav">
          <li v-for="(menu, index) in menuList" :key="index" :class="{ active : menuIndex === index }">
            <router-link :to="menu.url">
              <span class="nav-text" :class="mediaClass()" @click="menuSwitch(index)">{{ menu.text }}</span>
            </router-link>
          </li>
        </ul>
      </div>
      <div class="info-box" v-if="isOnline">
        <img :src="avatarUrl" alt="" v-on:click="dropdown">
        <ul class="nav-dropdown" v-bind:style="[displayStyles]">
          <li>
            <router-link class="nav-link" to="/candyRoom/myCandyOrder">订单</router-link>
          </li>
          <li>
            <a href="/admin/project" target="_blank">设置</a>
          </li>
          <li><a class="nav-link" @click="signout">注销</a></li>
        </ul>
        <span class="nickname mobile-hide">{{ mobile }}</span>
      </div>
      <div class="btn-box" :class="mediaClass()" v-else>
        <router-link to="/signin">
          <div class="signin btn mobile-btn"><span class="btn-text">登录</span></div>
        </router-link>
        <router-link to="/signup">
          <div class="signup btn mobile-btn"><span class="btn-text">注册</span></div>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isOnline: false,
      avatarUrl: '/static/img/avatar.png',
      mobile: 'yk_23723952234',
      menuIndex: 0,
      menuList: [
        {url: '/', text: '首页'},
        {url: '/projList', text: '发现'},
        {url: '/candyRoom', text: '余币宝'},
        {url: '/newlist', text: '资讯'}
      ],
      displayStyles: {
        display: 'none'
      }
    }
  },
  mounted () {
    this.updMenuIndex()

    // getCookie
    var userId = localStorage.getItem('userId')
    if (userId) {
      this.isOnline = true
      this.mobile = localStorage.getItem('mobile')
      var avatarUrl = localStorage.getItem('avatarUrl')
      if (avatarUrl) {
        this.avatarUrl = avatarUrl
      }
    }
  },
  watch: {
    '$route' () {
      this.updMenuIndex()
    }
  },
  methods: {
    menuSwitch (index) {
      this.menuIndex = index
    },
    updMenuIndex () {
      if (this.$route.path === '/') {
        this.menuIndex = 0
      } else if (this.$route.path.indexOf('/projList') === 0 || this.$route.path.indexOf('/projDetail') === 0) {
        this.menuIndex = 1
      } else if (this.$route.path.indexOf('/candyRoom') === 0) {
        this.menuIndex = 2
      } else if (this.$route.path.indexOf('/newlist') === 0 || this.$route.path.indexOf('/newdetail') === 0) {
        this.menuIndex = 3
      }
    },
    signout () {
      this.$http.post('/api/signout', {}).then((res) => {
        if (res.data.errcode === 0) {
          localStorage.removeItem('userId')
          localStorage.removeItem('mobile')
          localStorage.removeItem('avatarUrl')
          this.$router.go(0)
        }
      })
    },
    dropdown () {
      this.displayStyles.display = this.displayStyles.display === 'none' ? 'block' : 'none'
    }
  }
}
</script>

<style lang="scss" scoped>
.header {
  width: 100%;
  height: 60px;
  background-color: #FFF;
  display: flex;
  justify-content: center;
  .content {
    width: 1200px;
    height: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    padding: 0 10px;
    .logo {
      width: 161px;
      height: 30px;
      img {
        width: 100%;
        height: 100%;
      }
    }
    .nav {
      height: 100%;
      color: #8D8D8D;
      ul {
        height: 100%;
        display: flex;
        align-items: center;
        li {
          box-sizing: border-box;
          height: 100%;
          padding-top: 18px;
          margin-left: 50px;
          &.active {
            border-bottom: 5px solid #FFCF81;
            color: #4A4A4A;
          }
          .nav-text {
            &.media-mobile {
              font-size: 16px;
            }
            font-size: 18px;
            line-height: 25px;
          }
        }
      }
    }
    .btn-box {
      &.media-mobile {
        width: 120px;
        .btn {
          width: 45px;
        }
        .signup {
          margin: 0 0 0 6px;
        }
      }
      .btn {
        display: inline-block;
        width: 65px;
        height: 30px;
        border-radius: 4px;
        .btn-text {
          font-size: 16px;
          line-height: 30px;
          display: block;
          width: 100%;
          height: 100%;
          text-align: center;
        }
      }
      .signup {
        background-color: #282828;
        margin: 0 42px 0 16px;
        .btn-text {
          color: #FFCF81;
        }
      }
      .signin {
        border: 0.5px solid #CECECE;
        color: #8D8D8D;
      }
    }
    .info-box {
      display: flex;
      align-items: center;
      position: relative;
      .nickname {
        padding-left: 8px;
        font-size: 14px;
        color: #4A4A4A;
      }
      .nav-dropdown {
        display: none;
        box-sizing: border-box;
        max-height: calc(100vh - 61px);
        overflow-y: auto;
        position: absolute;
        top: 100%;
        background-color: #fff;
        padding: 10px 0;
        border: 1px solid #ddd;
        border-bottom-color: #ccc;
        text-align: left;
        border-radius: 4px;
        white-space: nowrap;
      }
      .nav-dropdown li {
        line-height: 1.8em;
        margin: 0;
        display: block;
      }
      .nav-dropdown a {
        padding: 0 24px 0 20px;
        cursor: pointer;
        &:hover {
          background-color: #FFCF81;
        }
      }
      .signout {
        cursor: pointer;
        background-color: #282828;
        margin: 0 42px 0 16px;
        width: 100px;
        height: 35px;
        border-radius: 4px;
        .btn-text {
          font-size: 16px;
          line-height: 35px;
          display: block;
          width: 100%;
          height: 100%;
          text-align: center;
          color: #FFCF81;
        }
      }
    }
  }
  @media screen and (max-width: 750px) {
    .middle-hide {
      display: none;
    }
  }
  @media screen and (max-width: 600px) {
    .mobile-hide {
      display: none;
    }
    .mobile-nav {
      li {
        margin-left: 20px !important;
      }
    }
    .content {
      /*justify-content: space-around !important;*/
    }
    .mobile-btn {
      width: 50px !important;
      height: 30px !important;
      border-radius: 4px !important;
      &.signout {
        width: 80px !important;
      }
      .btn-text {
        font-size: 16px !important;
        line-height: 30px !important;
        display: block !important;
        width: 100% !important;
        height: 100% !important;
        text-align: center !important;
      }
    }
  }
}
</style>
