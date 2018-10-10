<template>
  <aside class="site-sidebar" :class="'site-sidebar--' + sidebarLayoutSkin">
    <div class="site-sidebar__inner">

      <el-menu
        :default-active="menuActiveName || 'home'"
        :collapse="sidebarFold"
        :collapseTransition="false"
        class="site-sidebar__menu">

        <el-menu-item index="home" @click="$router.push({ name: 'home' })">
          <icon-svg name="shouye" class="site-sidebar__menu-icon"></icon-svg>
          <span slot="title">首页</span>
        </el-menu-item>

        <el-submenu index="demo">
          <template slot="title">
            <icon-svg name="duanxin" class="site-sidebar__menu-icon" ></icon-svg>
            <span>基本资料</span>
          </template>

          <el-menu-item index="demo-agent" @click="$router.push({ name: 'demo-agent' })">
            <icon-svg name="geren" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">代理商</span>
          </el-menu-item>

          <el-menu-item index="demo-custom" @click="$router.push({ name: 'demo-custom' })">
            <icon-svg name="editor" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">客户</span>
          </el-menu-item>

          <el-menu-item index="demo-ueditor" @click="$router.push({ name: 'demo-ueditor' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg> 
            <span slot="title">楼宇</span>
          </el-menu-item>

          <el-menu-item index="demo-equipment" @click="$router.push({ name: 'demo-equipment' })">
            <icon-svg name="shezhi" class="site-sidebar__menu-icon"></icon-svg> 
            <span slot="title">设备</span>
          </el-menu-item>

          <el-menu-item index="demo-gateway" @click="$router.push({ name: 'demo-gateway' })">
            <icon-svg name="oss" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">网关</span>
          </el-menu-item>

          <el-menu-item index="demo-model" @click="$router.push({ name: 'demo-model' })">
            <icon-svg name="menu" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">设备模板</span>
          </el-menu-item>
         </el-submenu>


          <el-submenu index="monitor">
           <template slot="title">
            <icon-svg name="shoucang" class="site-sidebar__menu-icon"></icon-svg>
            <span>实时监控</span>
          </template>

          <el-menu-item index="monitor-equip_status" @click="$router.push({ name: 'monitor-equip_status' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">设备状态</span>
          </el-menu-item>

          <el-menu-item index="monitor-graph" @click="$router.push({ name: 'monitor-graph' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">楼宇曲线</span>
          </el-menu-item>

          <el-menu-item index="monitor-power_data" @click="$router.push({ name: 'monitor-power_data' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">功率数据</span>
          </el-menu-item>

          <el-menu-item index="monitor-temperature" @click="$router.push({ name: 'monitor-temperature' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">温度数据</span>
          </el-menu-item>

          <el-menu-item index="monitor-pressure_data" @click="$router.push({ name: 'monitor-pressure_data' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">压力数据</span>
          </el-menu-item>

          <el-menu-item index="monitor-ueditor" @click="$router.push({ name: 'monitor-ueditor' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">设备地图</span>
          </el-menu-item>

         </el-submenu>


        <el-submenu index="tools">

          <template slot="title">
            <icon-svg name="shoucang" class="site-sidebar__menu-icon"></icon-svg>
            <span>工具</span>
          </template>

          <el-menu-item index="tools-account" @click="$router.push({ name: 'tools-account' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">代理商账户</span>
          </el-menu-item>

          <el-menu-item index="tools-export" @click="$router.push({ name: 'tools-export' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">导出用电量和天气</span>
          </el-menu-item>

          <el-menu-item index="tools-ueditor" @click="$router.push({ name: 'tools-ueditor' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">历史天气</span>
          </el-menu-item>

        </el-submenu>


        <sub-menu
          v-for="menu in menuList"
          :key="menu.menuId"
          :menu="menu"
          :dynamicMenuRoutes="dynamicMenuRoutes">
        </sub-menu>


        <el-submenu index="permission">

          <template slot="title">
            <icon-svg name="admin" class="site-sidebar__menu-icon"></icon-svg>
            <span>用户及权限</span>
          </template>

          <el-menu-item index="permission-user" @click="$router.push({ name: 'permission-user' })">
            <icon-svg name="geren" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">用户</span>
          </el-menu-item>

          <el-menu-item index="permission-ueditor" @click="$router.push({ name: 'permission-ueditor' })">
            <icon-svg name="geren" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">用户角色</span>
          </el-menu-item>

         </el-submenu>

         
         <el-submenu index="call">

          <template slot="title">
            <icon-svg name="tixing" class="site-sidebar__menu-icon"></icon-svg>
            <span>报警与消息</span>
          </template>

          <el-menu-item index="call-system" @click="$router.push({ name: 'call-system' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">系统消息</span>
          </el-menu-item>

          <el-menu-item index="call-calling" @click="$router.push({ name: 'call-calling' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">报警消息</span>
          </el-menu-item>

          <el-menu-item index="call-background" @click="$router.push({ name: 'call-background' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">后台任务</span>
          </el-menu-item>

          <el-menu-item index="call-ueditor" @click="$router.push({ name: 'call-ueditor' })">
            <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">报警设置</span>
          </el-menu-item>

         </el-submenu>


        

      </el-menu>        


    </div>

  </aside>
</template>

<script>
  import SubMenu from './main-sidebar-sub-menu'
  import { isURL } from '@/utils/validate'
  export default {
    data () {
      return {
        dynamicMenuRoutes: []
      }
    },
    components: {
      SubMenu
    },
    computed: {
      sidebarLayoutSkin: {
        get () { return this.$store.state.common.sidebarLayoutSkin }
      },
      sidebarFold: {
        get () { return this.$store.state.common.sidebarFold }
      },
      menuList: {
        get () { return this.$store.state.common.menuList },
        set (val) { this.$store.commit('common/updateMenuList', val) }
      },
      menuActiveName: {
        get () { return this.$store.state.common.menuActiveName },
        set (val) { this.$store.commit('common/updateMenuActiveName', val) }
      },
      mainTabs: {
        get () { return this.$store.state.common.mainTabs },
        set (val) { this.$store.commit('common/updateMainTabs', val) }
      },
      mainTabsActiveName: {
        get () { return this.$store.state.common.mainTabsActiveName },
        set (val) { this.$store.commit('common/updateMainTabsActiveName', val) }
      }
    },
    watch: {
      $route: 'routeHandle'
    },
    created () {
      this.menuList = JSON.parse(sessionStorage.getItem('menuList') || '[]')
      this.dynamicMenuRoutes = JSON.parse(sessionStorage.getItem('dynamicMenuRoutes') || '[]')
      this.routeHandle(this.$route)
    },
    methods: {
      // 路由操作
      routeHandle (route) {
        if (route.meta.isTab) {
          // tab选中, 不存在先添加
          var tab = this.mainTabs.filter(item => item.name === route.name)[0]
          if (!tab) {
            if (route.meta.isDynamic) {
              route = this.dynamicMenuRoutes.filter(item => item.name === route.name)[0]
              if (!route) {
                return console.error('未能找到可用标签页!')
              }
            }
            tab = {
              menuId: route.meta.menuId || route.name,
              name: route.name,
              title: route.meta.title,
              type: isURL(route.meta.iframeUrl) ? 'iframe' : 'module',
              iframeUrl: route.meta.iframeUrl || ''
            }
            this.mainTabs = this.mainTabs.concat(tab)
          }
          this.menuActiveName = tab.menuId + ''
          this.mainTabsActiveName = tab.name
        }
      }
    }
  }
</script>
