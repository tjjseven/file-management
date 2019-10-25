<template>
  <div :class="classObj" class="app-wrapper">
    <div v-if="device==='mobile'&&sidebar.opened" class="drawer-bg" @click="handleClickOutside" />
    <div class="t_nav">
      <logo v-if="showLogo" :collapse="isCollapse" />
      <div class="right-menu">
        <HeaderSearch class="right-menu-item"/>
        <el-badge is-dot class="item right-menu-item">
          <i class="el-icon-bell nav_icon" title="消息通知"></i>
        </el-badge>
        <i class="el-icon-question nav_icon right-menu-item" title="帮助中心"></i>
        <i class="el-icon-edit-outline nav_icon right-menu-item" title="意见反馈"></i>
        <!--<ThemePicker class="right-menu-item"/>-->
        <el-dropdown class="avatar-container right-menu-item" trigger="click">
            <div class="avatar-wrapper">
              <img :src="avatar+'?imageView2/1/w/80/h/80'" class="user-avatar">
              <i class="el-icon-caret-bottom" />
            </div>
            <el-dropdown-menu slot="dropdown" class="user-dropdown">
              <router-link to="/">
                <el-dropdown-item>
                  Home
                </el-dropdown-item>
              </router-link>
              <a target="_blank" href="">
                <el-dropdown-item>Github</el-dropdown-item>
              </a>
              <el-dropdown-item divided>
                <span style="display:block;" @click="logout">Log Out</span>
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
      </div>
    </div>

    <sidebar class="sidebar-container" />

    <div class="main-container" :class="{'main-tags-view':needTagsView}">
      <div :class="{'fixed-header':fixedHeader}">
        <navbar />
        <tags-view v-if="needTagsView" />
      </div>
      <right-panel v-if="showSettings">
        <settings />
      </right-panel>
      <app-main />
    </div>
  </div>
</template>

<script>
import Logo from './components/Sidebar/Logo'
import RightPanel from '@/components/RightPanel'
import { mapState, mapGetters } from 'vuex'
import HeaderSearch from '@/components/HeaderSearch'
import { Navbar, Sidebar, AppMain, TagsView, Settings } from './components'
import ResizeMixin from './mixin/ResizeHandler'
import ThemePicker from '@/components/ThemePicker'
export default {
  name: 'Layout',
  components: {
    Navbar,
    Sidebar,
    AppMain,
    ThemePicker,
    HeaderSearch,
    TagsView,
    Settings,
    RightPanel,
    Logo
  },
  mixins: [ResizeMixin],
  computed: {
    ...mapGetters([
      'avatar'
    ]),
    ...mapState({
      needTagsView: state => state.settings.tagsView,
      showSettings: state => state.settings.showSettings
    }),
    sidebar() {
      return this.$store.state.app.sidebar
    },
    device() {
      return this.$store.state.app.device
    },
    fixedHeader() {
      return this.$store.state.settings.fixedHeader
    },
    classObj() {
      return {
        hideSidebar: !this.sidebar.opened,
        openSidebar: this.sidebar.opened,
        withoutAnimation: this.sidebar.withoutAnimation,
        mobile: this.device === 'mobile'
      }
    },
    showLogo() {
      return this.$store.state.settings.sidebarLogo
    },
    isCollapse() {
      return !this.sidebar.opened
    }
  },
  methods: {
    handleClickOutside() {
      this.$store.dispatch('app/closeSideBar', { withoutAnimation: false })
    },
    async logout() {
      await this.$store.dispatch('user/logout')
      this.$router.push(`/login?redirect=${this.$route.fullPath}`)
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "~@/styles/mixin.scss";
  @import "~@/styles/variables.scss";

  .app-wrapper {
    @include clearfix;
    position: relative;
    height: 100%;
    width: 100%;
    &.mobile.openSidebar{
      position: fixed;
      top: 0;
    }
  }
  .drawer-bg {
    background: #000;
    opacity: 0.3;
    width: 100%;
    top: 0;
    height: 100%;
    position: absolute;
    z-index: 999;
  }

  .fixed-header {
    position: fixed;
    top: 50px;
    right: 0;
    z-index: 9;
    width: calc(100% - #{$sideBarWidth});
    transition: width 0.28s;
  }

  .hideSidebar .fixed-header {
    width: calc(100% - 54px)
  }

  .mobile .fixed-header {
    width: 100%;
  }



  .t_nav{
    background: #242f42;
    width: 100%;
    height: 50px;
    position: fixed;
    top: 0;
    z-index: 99;
    /*display: none;*/
  }
  #app .sidebar-container{
    top: 50px;
  }
  #app .main-container{
    margin-top: 50px;
  }
  .right-menu {
    float: right;
    height: 100%;
    display: flex;
    align-items: center;
    &:focus {
      outline: none;
    }
    .nav_icon{
      font-size: 20px;
      color: #fff;
      cursor:pointer;
    }
    .right-menu-item {
      margin: 0 8px;
      /*height: 100%;*/
      /*font-size: 18px;*/
      /*color: #5a5e66;*/
      /*vertical-align: text-bottom;*/
      &.hover-effect {
        cursor: pointer;
        transition: background .3s;

        &:hover {
          background: rgba(0, 0, 0, .025)
        }
      }
    }

    .avatar-container {
      margin-right: 30px;
      img{
        float: left;
      }
      .avatar-wrapper {
        position: relative;

        .user-avatar {
          cursor: pointer;
          width: 30px;
          height: 30px;
          border-radius: 5px;
        }

        .el-icon-caret-bottom {
          cursor: pointer;
          position: absolute;
          right: -17px;
          top: 20px;
          font-size: 12px;
        }
      }
    }
  }
  .main-tags-view>.fixed-header+.app-main{
    padding-top: 87px;
  }
</style>
