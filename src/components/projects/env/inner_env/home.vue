<template>
  <div class="project-home"
       v-loading="loading"
       element-loading-text="加载中..."
       element-loading-spinner="iconfont iconfont-loading iconrongqi">
    <div v-if="loading"
         class="no-show">
      <img src="@assets/icons/illustration/environment.svg"
           alt="" />
    </div>
    <router-view v-else></router-view>
  </div>
</template>
<script>
import bus from '@utils/event_bus'
import { mapGetters } from 'vuex'
export default {
  data () {
    return {
      loading: true,
      jumpPath: ''
    }
  },
  methods: {
    async getProducts () {
      const availableProducts = this.currentProjectProductList
      this.loading = false
      if (availableProducts.envs.length > 0) {
        this.jumpPath = `/v1/projects/detail/${this.projectName}/envs/detail?envName=${availableProducts.envs[0]}`
      } else if (availableProducts.envs.length === 0) {
        this.jumpPath = `/v1/projects/detail/${this.projectName}/envs/create`
      }
      if (this.$route.params.service_name || this.$route.query.envName) {
        return
      }
      this.jumpPath && this.$router.push(this.jumpPath)
    }
  },
  computed: {
    projectName () {
      return this.$route.params.project_name
    },
    currentProjectProductList () {
      return this.productList.find(element => {
        return element.name === this.projectName
      })
    },
    ...mapGetters([
      'productList'
    ])
  },
  beforeRouteUpdate (to, from, next) {
    if (!this.jumpPath || to.meta.title === '创建环境') {
      next()
    } else if (!to.params.service_name && !to.query.envName) {
      next({ path: this.jumpPath })
    } else {
      next()
    }
  },
  mounted () {
    bus.$emit('set-topbar-title', { title: '', breadcrumb: [] })
    bus.$emit('set-sub-sidebar-title', {
      title: this.projectName,
      url: `/v1/projects/detail/${this.projectName}`,
      routerList: [
        { name: '工作流', url: `/v1/projects/detail/${this.projectName}/pipelines` },
        { name: '集成环境', url: `/v1/projects/detail/${this.projectName}/envs` },
        { name: '服务', url: `/v1/projects/detail/${this.projectName}/services` },
        { name: '构建', url: `/v1/projects/detail/${this.projectName}/builds` },
        { name: '测试', url: `/v1/projects/detail/${this.projectName}/test` }]
    })
    if (this.$route.query.outer) {
      this.loading = false
      return
    }
    this.getProducts()
  }
}
</script>

<style lang="less" >
.project-home {
  position: relative;
  display: flex;
  flex: 1;
  padding: 15px 20px;
  overflow: hidden;

  .no-show {
    margin: auto;

    img {
      width: 460px;
      height: 460px;
    }

    p {
      color: #606266;
      font-size: 15px;
    }
  }
}
</style>
