<template>
  <div class="search-box">
    <a-input-search></a-input-search>
  </div>
  <a-float-button-group shape="square" class="button-group">
    <a-float-button @click="startDraw">
      <template #icon>
        <BorderOutlined />
      </template>
    </a-float-button>
  </a-float-button-group>
  <div id="container"></div>
</template>

<script setup lang="ts">

import { onMounted, onUnmounted, h } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import { BorderOutlined, AppstoreOutlined } from '@ant-design/icons-vue';

window._AMapSecurityConfig = {
  securityJsCode: "8b57d8d2fbf90d8be55aa426964af20e",
};

let map = null;
let polygonEditor = null;
let polygon = null;

// 开始绘制
const startDraw = () => {
  console.log('开始绘制');
  if (polygonEditor) {
    polygonEditor.open();
  }
};

// 结束绘制
const endDraw = () => {
  if (polygonEditor) {
    polygonEditor.close();
  }
}

onMounted(() => {
  AMapLoader.load({
    key: "7d42259f8dbff585d140338d61b08e1f", // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: ['AMap.ToolBar', 'AMap.Geolocation', 'AMap.PolygonEditor'], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {
      map = new AMap.Map("container", {
        // 设置地图容器id
        viewMode: "2D", // 是否为3D地图模式
        zoom: 11, // 初始化地图级别
        center: [116.397428, 39.90923], // 初始化地图中心点位置
      });

      // 添加缩放工具条
      const toolbar = new AMap.ToolBar({
        position: {
          bottom: '80px',
          right: '35px'
        }
      });

      // 添加定位
      const geolocation = new AMap.Geolocation({
        position: {
          bottom: '30px',
          right: '35px'
        }
      })

      // 创建多边形实例
      polygon = new AMap.Polygon({
        path: [],
        strokeColor: '#FF33FF',
        strokeWeight: 2,
        strokeOpacity: 0.8,
        fillColor: '#1791fc',
        fillOpacity: 0.4,
      });
      // 将多边形添加到地图
      map.add(polygon);

      // 创建编辑器实例
      polygonEditor = new AMap.PolygonEditor(map, polygon);

      // 监听编辑器事件
      polygonEditor.on('add', (data) => {
        console.log('新增顶点', data);
      });

      polygonEditor.on('adjust', (data) => {
        console.log('调整顶点', data);
      });

      map.addControl(toolbar);
      map.addControl(geolocation);

    })
    .catch((e) => {
      console.log(e);
    });
});

onUnmounted(() => {
  map?.destroy();
});


</script>


<style>
.search-box {
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 1000;
}

.button-group {
  right: 29px;
  bottom: 280px;
  box-shadow: 0 0 3px rgba(0, 0, 0, 0.5);
}

#container {
  width: 100%;
  height: 100%;
  /* border: 2px solid red; */
  background-color: rgba(255, 0, 0, 0.1);
}
</style>
