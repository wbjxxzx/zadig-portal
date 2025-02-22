<template>
  <div class="kr-top-bar">
    <div class="top-bar-content">
      <div class="kr-top-bar-start">
        <span v-if="content.title"
              class="kr-topbar-title">{{content.title}}</span>
        <el-breadcrumb v-if="content.breadcrumb && content.breadcrumb.length > 0"
                       separator=">">
          <el-breadcrumb-item v-for="(item,index) in content.breadcrumb"
                              :to="item.url"
                              :key="index">{{item.title}}</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <div class="kr-top-bar-end">
        <el-popover placement="bottom"
                    popper-class="help-droplist"
                    trigger="click">
          <ul class="dropdown-menu"
              uib-dropdown-menu="">
            <li>
              <a href="https://docs.koderover.com/zadig/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>文档站</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/quick-start/introduction/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>入门</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/examples/voting/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>最佳实践</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/workflow/trigger/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>工作流</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/env/service/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>集成环境</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/project/service/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>项目管理</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/delivery/artifact/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>交付中心</span>
              </a>
            </li>
            <li role="separator"
                class="divider"></li>
            <li>
              <a href="https://docs.koderover.com/zadig/settings/codehost/gitlab/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>系统设置</span>
              </a>
            </li>
            <li>
              <a href="https://docs.koderover.com/zadig/api/usage/"
                 target="_blank">
                <i class="icon el-icon-link"></i>
                <span>开发者中心</span>
              </a>
            </li>
          </ul>
          <span slot="reference"
                class="help">
            <i class="el-icon-question"></i>
            <span class="text">文档</span></span>
        </el-popover>
        <span>
          <notification class="icon notify"></notification>
        </span>

        <div class="nav nav-item-bottom user-profile">
          <el-popover placement="bottom"
                      width="240"
                      popper-class="dropdown-menu"
                      trigger="click">
            <div class="flex">
              <div class="profile-menu__list">
                <ul class="profile-list profile-list__with-icon">
                  <li class="profile-list__item profile-list__item-nested">
                    <div class="title">
                      <i class="iconfont iconzhanghu"></i>
                      <span class="profile-list__text">用户名</span>
                    </div>
                    <ul class="content profile-list">
                      <li class="profile-list__item active">
                        <span>{{userName}}</span>
                        <el-tag v-if="role.includes('admin')"
                                size="mini"
                                type="primary"
                                effect="plain"
                                >管理员</el-tag>
                        <el-tag v-else
                                size="mini"
                                type="primary"
                                effect="plain">普通用户</el-tag>
                      </li>
                    </ul>
                  </li>
                </ul>
                <ul v-if="role.includes('admin')"
                    class="profile-list profile-list__with-icon user-settings">
                  <router-link to="/v1/users/account/manage">
                    <li class="profile-list__item">
                      <i class="iconfont icongeren"></i>
                      <span class="profile-list__text">用户管理</span>
                    </li>
                  </router-link>
                  <router-link to="/v1/system">
                    <li class="profile-list__item">
                      <i class="iconfont iconicon_jichengguanli"></i>
                      <span class="profile-list__text">系统设置</span>
                    </li>
                  </router-link>
                </ul>
                <ul class="profile-list profile-list__with-icon">
                  <router-link to="/v1/profile/info">
                    <li class="profile-list__item">
                      <i class="iconfont iconfenzucopy"></i>
                      <span class="profile-list__text">账号设置</span>
                    </li>
                  </router-link>
                  <li class="profile-list__item profile-list__with-icon">
                    <i class="iconfont icondengchu"></i>
                    <span @click="logOut"
                          class="profile-list__text logout">登出账号</span>
                  </li>
                </ul>
              </div>
            </div>
            <div slot="reference"
                 class="dropdown-menu-reference">
              <img src="@assets/icons/others/profile.png"
                   class="menu-avatar"
                   alt="">
              <span class="username">
                <span>{{userName}}</span>
                <i class="el-icon-arrow-down el-icon--right"></i>
              </span>
            </div>
          </el-popover>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import notification from './common/notification.vue'
import mixin from '@utils/topbar_mixin'
import bus from '@utils/event_bus'
import { mapState } from 'vuex'
export default {
  data () {
    return {
      content: {
        title: '',
        breadcrumb: [],
        productList: []
      }
    }
  },
  computed: {
    ...mapState({
      role: (state) => state.login.role,
      userInfo: (state) => state.login.userinfo
    }),
    userName () {
      // 系统用户
      if (this.userInfo.identityType === 'system') {
        if (this.userInfo.name) {
          return `${this.userInfo.name}(${this.userInfo.account})`
        } else {
          return this.userInfo.account
        }// 第三方登录
      } else if (this.userInfo.preferred_username) {
        return `${this.userInfo.name}(${this.userInfo.preferred_username})`
      } else {
        return this.userInfo.name
      }
    }
  },
  methods: {
    async logOut () {
      await this.$store.dispatch('LOGINOUT')
      this.$router.push('/signin')
    },
    handleCommand (command) {
      if (command === 'logOut') {
        this.logOut()
      }
    },
    changeTitle (params) {
      this.content = params
    }
  },
  created () {
    bus.$on('set-topbar-title', (params) => {
      this.changeTitle(params)
    })
  },
  components: {
    notification
  },
  mixins: [mixin]
}
</script>
<style lang="less">
.dropdown-menu-reference {
  display: flex;
  align-items: center;
  color: #44504f;
  line-height: 60px;
  cursor: pointer;

  .username {
    display: inline-block;
    margin-left: 10px;
    font-size: 15px;
  }

  .el-icon--right {
    margin-left: 25px;
  }
}

.help-droplist {
  .dropdown-menu {
    margin: 0 2px;
    padding: 8px 0;
    list-style: none;

    .divider {
      height: 1px;
      margin: 9px 0;
      overflow: hidden;
      background-color: #e5e5e5;
    }

    li {
      & > a {
        display: block;
        clear: both;
        padding: 3px 20px;
        padding: 4px 0 4px 5px;
        color: #333;
        font-weight: normal;
        line-height: 1.42857143;
        white-space: nowrap;

        .icon {
          position: relative;
          margin-right: 5px;
          font-size: 16px;
        }
      }

      &:hover {
        color: #262626;
        text-decoration: none;
        background-color: #f5f5f5;

        & > a {
          color: #1989fa;
        }
      }
    }
  }
}

.flex {
  display: flex;

  .profile-menu__list {
    width: 100%;
    padding: 8px 21px 2px 14px;

    .profile-list {
      margin: 0;
      padding: 10px 0;
      list-style-type: none;
      border-bottom: 1px solid #dbdbdb;

      .profile-list__item-nested {
        padding: 0;

        .title {
          margin-bottom: 5px;
        }
      }

      .profile-list__item {
        padding: 5px 0;
        font-size: 13px;

        .profile-list__text {
          color: #434548;
        }

        &:hover {
          .profile-list__text {
            color: #1989fa;
          }
        }

        i {
          display: inline-block;
          width: 20px;
          margin-right: 10px;
          color: #3a8ee6;
          font-size: 16px;
          text-align: center;
        }

        .logout {
          cursor: pointer;
        }
      }

      &.content {
        padding: 0;
        border-bottom: none;

        .profile-list__item {
          font-weight: normal;
        }
      }

      &:last-child {
        border-bottom: none;
      }
    }
  }
}

.kr-top-bar {
  top: 0;
  right: 0;
  left: 66px;
  z-index: 1010;
  height: 60px;
  padding: 0 20px;
  color: #44504f;
  font-size: 14px;
  background-color: #fff;
  border-bottom: 1px solid #ebedef;

  .top-bar-content {
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    height: 100%;
    -webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-box-pack: justify;
    -ms-flex-pack: justify;

    .kr-top-bar-start {
      display: flex;
      flex: 0 1 auto;
      flex-basis: auto;
      flex-grow: 1;
      flex-shrink: 1;
      align-items: center;
      justify-content: flex-start;
      min-width: 0;
      margin-right: 10px;

      span {
        &.kr-topbar-title {
          display: block;
          flex-grow: 0;
          overflow: hidden;
          font-weight: 300;
          font-size: 21px;
          white-space: nowrap;
          text-overflow: ellipsis;
        }
      }

      .el-breadcrumb {
        font-size: 16px;
      }
    }

    .kr-top-bar-end {
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex;
      flex-grow: 0;
      flex-shrink: 0;
      align-items: center;
      justify-content: space-between;
      -webkit-box-align: center;
      -ms-flex-align: center;
      -webkit-box-pack: justify;
      -ms-flex-pack: justify;
      -webkit-box-flex: 0;
      -ms-flex-positive: 0;
      -ms-flex-negative: 0;

      * {
        max-height: 100%;
      }

      .icon {
        color: #4c4c4c;
        font-size: 20px;
        cursor: pointer;

        &:hover {
          color: #1989fa;
        }
      }

      .notify {
        font-size: 20px;
        line-height: 60px;
      }

      .system-summary {
        margin-right: 30px;
        line-height: 15px;
      }

      .help {
        display: block;
        margin-right: 45px;
        line-height: 60px;
        cursor: pointer;

        .text {
          display: inline-block;
          line-height: 60px;
        }
      }

      .system-summary,
      .help {
        color: #4c4c4c;
        font-size: 15px;

        i {
          display: inline-block;
          margin-right: 5px;
          color: rgba(0, 0, 0, 0.19);
          font-size: 20px;
          line-height: 60px;
        }

        &:hover {
          color: #1989fa;

          i {
            color: #1989fa;
          }
        }
      }

      .user-profile {
        margin-left: 80px;

        .menu-avatar {
          width: 30px;
          height: 30px;
          border-radius: 50%;
        }
      }
    }
  }
}

.el-select-dropdown.el-popper {
  .el-scrollbar {
    .el-select-dropdown__item {
      .k8s-product-option {
        position: relative;

        span {
          padding-right: 20px;
        }

        i {
          position: absolute;
          right: 0;
          padding-left: 8px;
          line-height: 34px;
          visibility: hidden;
          cursor: pointer;
        }

        &:hover {
          i {
            visibility: visible;
          }
        }
      }
    }
  }
}
</style>
