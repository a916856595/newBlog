<template>
  <div class="header-component">
    <div class="menu-list-box">
      <div class="w">
        <ul class="menu-list clearfix">
          <router-link tag="li" to="/home" class="fl hover-button">
            <span>首页</span>
          </router-link>
          <router-link tag="li" to="/register" class="fr hover-button not-mobile" v-if="!accountInfo">
            <span>注册</span>
          </router-link>
          <router-link tag="li" to="/login" class="fr hover-button not-mobile" v-if="!accountInfo">
            <span>登录</span>
          </router-link>
          <router-link tag="li" to="/userCenter" class="fr hover-button not-mobile" v-if="accountInfo">
            <span v-text="accountInfo.nickName"></span>
          </router-link>
          <li class="fr hover-button mobile" @click="toggleSideMenu">
            <i class="icon iconfont iconmenu"></i>
          </li>
        </ul>
      </div>
    </div>
    <div class="menu-list-row-mask" @click="closeSideMenu()" v-if="isShowSideMenu"></div>
    <div :class="['menu-list-row-box', 'mobile', {'show-side-menu': isShowSideMenu}]">
      <ul class="menu-list-row">
        <li @click="closeSideMenu('/register')" class="hover-button" v-if="!accountInfo">
          <span>注册</span>
        </li>
        <li tag="li" @click="closeSideMenu('/login')" class="hover-button" v-if="!accountInfo">
          <span>登录</span>
        </li>
        <li tag="li" @click="closeSideMenu('/userCenter')" class="hover-button" v-if="accountInfo">
          <span v-text="accountInfo.nickName"></span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'header-component',
  data () {
    return {
      isShowSideMenu: false,
      accountInfo: null
    }
  },
  methods: {
    toggleSideMenu () {
      this.isShowSideMenu = !this.isShowSideMenu;
    },
    closeSideMenu (path) {
      this.isShowSideMenu = false;
      if (path) this.$router.push(path);
    }
  },
  computed: {
    tempAccountInfo () {
      return this.$store.state.tempAccountInfo;
    }
  },
  watch: {
    tempAccountInfo (newValue) {
      console.log(newValue)
      if (newValue && newValue.id && newValue.nickName) {
        this.accountInfo = newValue;
      } else {
        this.accountInfo = null;
      }
    }
  }
}
</script>

<style lang="scss">
  .header-component {
    .menu-list-box {
      z-index: 99;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      background-color: #fff;
      color: #000;
      border-bottom: 1px solid $border-color;
    }
    .menu-list, .menu-list-row {
      li {
        padding: $header-padding $header-padding * 2;
        span, i, a {
          line-height: $header-line-height;
          text-decoration: none;
          color: #000;
        }
        &:hover {
          span, i, a {
            color: $color-theme;
          }
        }
      }
    }
    .menu-list-row-mask {
      position: fixed;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      z-index: 97;
    }
    .menu-list-row-box {
      overflow: hidden;
      background-color: #fff;
      z-index: 98;
      padding-top: $header-height + 1px;
      position: fixed;
      left: 0;
      top: 0;
      width: 120px;
      height: 100%;
      border-right: $border;
      transform: translateX(-100%);
      transition: transform $transition-time;
      &.show-side-menu {
        transform: translateX(0);
      }
    }
    .menu-list-row {
      li {
        border-bottom: $border;
      }
    }
  }
</style>