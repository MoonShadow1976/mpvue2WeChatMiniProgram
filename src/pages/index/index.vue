<template>
  <div @click="clickHandle">
    <!-- 用户信息 -->
    <div class="userinfo" @click="bindViewTap">
      <img class="userinfo-avatar" src="/static/images/user.png" background-size="cover" />

      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div>

    <!-- 地图容器 -->
    <div id="map" class="map-container">
      <!-- 地图内容：使用微信小程序的腾讯地图api -->
      <map
        id="TXmap"
        :longitude="longitude"
        :latitude="latitude"
        scale="18"
        show-location
        style="width: 100%; height: 100vh;"
        @regionchange="regionchange">
      </map>
    </div>

    <!-- 定位和缩放按钮 -->
    <div class="button-container">
      <button type="primary" size="mini" @click="locateUser">定位</button>
      <button type="primary" size="mini" @click="zoomIn">放大</button>
      <button type="primary" size="mini" @click="zoomOut">缩小</button>
    </div>

    <!-- 底部导航 -->
    <div class="bottom-nav">
      <div class="usermotto">
        <div class="user-motto">
          <card :text="motto"></card>
        </div>
      </div>
      <a href="/pages/counter/main" class="counter">其他数据界面</a>
      <!-- MapServer 容器 -->
      <div class="map-server-container">
        <!-- 增加两个按钮：查询，路径追踪，按钮并行排布 -->
        <button type="primary" size="mini" @click="query">查询</button>
        <button type="primary" size="mini" @click="track">路径追踪</button>
      </div>
    </div>
  </div>
</template>

<script>
import card from '@/components/card'

export default {
  data () {
    return {
      longitude: 0, // 初始经度
      latitude: 0, // 初始纬度
      scale: 5, // 地图缩放级别
      motto: 'MapServer',
      userInfo: {
        nickName: 'mpvue',
        avatarUrl: 'http://mpvue.com/assets/img/logo.0aaccdfd.png'
      }
    }
  },

  components: {
    card
  },

  methods: {
    bindViewTap () {
      const url = '../logs/main'
      if (mpvuePlatform === 'wx') {
        mpvue.switchTab({ url })
      } else {
        mpvue.navigateTo({ url })
      }
    },
    clickHandle (ev) {
      console.log('clickHandle:', ev)
      // throw {message: 'custom test'}
    },
    initMap () {
      this.mapCtx = wx.createMapContext('TXmap', this)
    },
    getCenterLocation () {
      this.mapCtx.getCenterLocation({
        success: (res) => {
          console.log(res.latitude)
          console.log(res.longitude)
        }
      })
    },
    locateUser () {
      // 实现定位用户坐标的功能
      wx.getLocation({
        type: 'gcj02 ',
        success (res) {
          this.latitude = res.latitude
          this.longitude = res.longitude
          console.log('坐标：', this.latitude, this.longitude)
          // 使用 wx.createMapContext 获取 map 上下文
          const mapCtx = wx.createMapContext('TXmap')
          // 使用获取到的经纬度更新地图位置
          mapCtx.moveToLocation({
            latitude: this.latitude,
            longitude: this.longitude
          })
        },
        fail () {
          console.log('定位失败')
        }
      })
    },
    zoomIn () {
      // 实现地图放大的功能
      this.scale += 1
      this.mapCtx = wx.createMapContext('TXmap', this)
      this.mapCtx.scaleTo(this.scale)
    },
    zoomOut () {
      // 实现地图缩小的功能
      this.scale = this.scale - 1
      // 使用 wx.createMapContext 获取 map 上下文
      const mapCtx = wx.createMapContext('TXmap')
      // 使用获取到的经纬度更新地图位置
      mapCtx.moveToLocation({
        scale: this.scale
      })
      console.log('缩放:', this.scale)
    }
  },

  created () {
    // let app = getApp()
  }
}
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.userinfo-avatar {
  width: 100rpx;
  height: 100rpx;
  margin: 1rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

/* 地图容器样式 */
.map-container {
  position: absolute;
  width: 100%;
  height: 100%;
}

/* 定位按钮样式 */
.button-container {
  position: fixed;
  right: 20rpx;
  top: 50%;
  display: flex;
  flex-direction: column;
}

/* 底部导航样式 */
.bottom-nav {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  position: absolute;
  bottom: 0rpx;
  width: 100%;
  padding: 0 20rpx;
}

.usermotto {
  margin-top: 0px;
}
</style>
