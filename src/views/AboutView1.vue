<template>
  <div class="mian-box" ref="container">
    <div class="gantt-box" ref="ganttContainer"></div>
    <div class="controls">
      <div class="zoom-control">
        <div class="zoom-title">缩放控制器</div>
        <div class="slider-container">
          <input type="range" id="scaleSlider" min="40" max="100" value="100" orient="vertical">
        </div>
        <div class="zoom-value">100%</div>
      </div>
    </div>
    <iframe 
      class="background-iframe"
      :src="backgroundUrl"
      frameborder="0"
      allowfullscreen
    ></iframe>
  </div>
</template>

<script>
// import gantt from "dhtmlx-gantt";
// import "dhtmlx-gantt/codebase/dhtmlxgantt.css"

import {gantt} from '../../codebase1/dhtmlxgantt'
import "../../codebase1/dhtmlxgantt.css"

// import { Gantt} from "@dhx/trial-gantt";
// import "@dhx/trial-gantt/codebase/dhtmlxgantt.css";

export default {
  name: 'AboutView',
  data() {
    return {
      backgroundUrl: 'http://47.111.147.202:10090/video.html',
      tasks: {
        data: [
          { id: 11, text: "Project #1", type: "project", progress: 0.6, open: true },

          { id: 12, text: "Task #1", start_date: "03-04-2023", duration: 5, parent: 11, progress: 1, open: true },
          { id: 13, text: "Task #2", start_date: "03-04-2023", type: "project", parent: 11, progress: 0.5, open: true },
          { id: 14, text: "Task #3", start_date: "02-04-2023", duration: 6, parent: 11, progress: 0.8, open: true },
          { id: 15, text: "Task #4", type: "project", parent: 11, progress: 0.2, open: true },
          { id: 16, text: "Final milestone", start_date: "15-04-2023", type: "milestone", parent: 11, progress: 0, open: true },

          { id: 17, text: "Task #2.1", start_date: "03-04-2023", duration: 2, parent: 13, progress: 1, open: true },
          { id: 18, text: "Task #2.2", start_date: "06-04-2023", duration: 3, parent: 13, progress: 0.8, open: true },
          { id: 19, text: "Task #2.3", start_date: "10-04-2023", duration: 4, parent: 13, progress: 0.2, open: true },
          { id: 20, text: "Task #2.4", start_date: "10-04-2023", duration: 4, parent: 13, progress: 0, open: true },
          { id: 21, text: "Task #4.1", start_date: "03-04-2023", duration: 4, parent: 15, progress: 0.5, open: true },
          { id: 22, text: "Task #4.2", start_date: "03-04-2023", duration: 4, parent: 15, progress: 0.1, open: true },
          { id: 23, text: "Mediate milestone", start_date: "14-04-2023", type: "milestone", parent: 15, progress: 0, open: true }
        ],
        links: [
          { id: 10, source: 11, target: 12, type: 1 },
          { id: 11, source: 11, target: 13, type: 1 },
          { id: 12, source: 11, target: 14, type: 1 },
          { id: 13, source: 11, target: 15, type: 1 },
          { id: 14, source: 23, target: 16, type: 0 },
          { id: 15, source: 13, target: 17, type: 1 },
          { id: 16, source: 17, target: 18, type: 0 },
          { id: 17, source: 18, target: 19, type: 0 },
          { id: 18, source: 19, target: 20, type: 0 },
          { id: 19, source: 15, target: 21, type: 2 },
          { id: 20, source: 15, target: 22, type: 2 },
          { id: 21, source: 15, target: 23, type: 0 }
        ]
      },
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.initGantt();
    })
  },
  beforeUnmount() {
    // 清理 Gantt 资源
    if (gantt && gantt.destroy) {
      gantt.destroy();
    }
    // this.gantt.destructor();
  },
  methods: {
    initGantt() {
      // let gantt = Gantt.getGanttInstance();
      gantt.plugins({
        marker: true,
        tooltip: true
      });
      gantt.i18n.setLocale('cn')


      gantt.init(this.$refs.ganttContainer);
      gantt.parse(this.tasks)

      // var firstTaskDate = gantt.getTaskByIndex(0).start_date;
      var firstTaskDate = new Date('2023, 04, 03')
      console.log('firstTaskDate', firstTaskDate)
      var todayMarker = gantt.addMarker({
        start_date: firstTaskDate,
        css: "today",
        text: "Today"
      });
      // 缩放控制功能
      const slider = document.getElementById("scaleSlider");
      const zoomValue = document.querySelector(".zoom-value");
      const ganttContainer = this.$refs.ganttContainer;
      
      // 保存原始尺寸
      const originalWidth = ganttContainer.offsetWidth;
      const originalHeight = ganttContainer.offsetHeight;
      
      // 更新缩放值显示
      function updateZoomValue(value) {
        zoomValue.textContent = `${value}%`;
        
        // 添加动画效果
        zoomValue.style.transform = "scale(1.2)";
        setTimeout(() => {
            zoomValue.style.transform = "scale(1)";
        }, 200);
      }
      
      // 应用缩放
      function applyZoom(scale) {
        const newWidth = originalWidth * (scale / 100);
        const newHeight = originalHeight * (scale / 100);
        
        ganttContainer.style.width = `${newWidth}px`;
        ganttContainer.style.height = `${newHeight}px`;
        
        // 更新滑块值
        slider.value = scale;
        updateZoomValue(scale);
        
        // 刷新Gantt图表
        setTimeout(() => gantt.render(), 100);
      }
      
      // 滑块事件监听
      slider.addEventListener("input", function() {
        const scale = parseInt(this.value);
        applyZoom(scale);
      });
    },
  }
}
</script>
<style>
/* @import "@dhx/trial-gantt/codebase/dhtmlxgantt.css"; */
.mian-box {
  display: flex;
  height: 100vh;
  width: 100%;
  overflow: hidden;
}
.gantt-box {
  width: 100%;
  height: 100%;
  z-index: 1;
  /* margin-right: 30px; */
}
.controls {
  /* width: 100px; */
  display: flex;
  flex-direction: column;
  align-items: center;
  /* gap: 20px; */
  z-index: 1;
}
/* .gantt_cal_light {
  z-index: 10 !important;
} */

.zoom-control {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  padding: 30px 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.15);
}

.zoom-title {
  font-size: 18px;
  margin-bottom: 20px;
  font-weight: 600;
}

.slider-container {
  height: 180px;
  display: flex;
  align-items: center;
  position: relative;
}

#scaleSlider {
  -webkit-appearance: slider-vertical;
  width: 10px;
  height: 180px;
  background: linear-gradient(to top, #4e54c8, #8f94fb);
  border-radius: 5px;
  outline: none;
  cursor: pointer;
  padding: 0 5px;
}

#scaleSlider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background: #fff;
  border: 2px solid #4e54c8;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
}

.zoom-value {
  background: rgba(255, 255, 255, 0.15);
  padding: 12px 20px;
  border-radius: 30px;
  font-size: 16px;
  font-weight: bold;
  margin-top: 20px;
  min-width: 60px;
  text-align: center;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.background-iframe {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0; /* 将iframe置于底层 */

}

.gantt_layout_cell {
  /* background-image: url('../assets/images/1.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed; */
  background: transparent;
}
.gantt_grid_scale,
.gantt_grid_data {
  user-select: none !important;
}
.gantt_grid_head_cell {
  cursor: default !important;
}
.gantt_task_scale, .gantt_grid, .gantt_grid_head_cell, .gantt_task_row, .gantt_grid_scale {
  background-color: rgba(0, 0 ,0, .05) !important;
  color: #fff !important;
}
.gantt_tree_content, .gantt_scale_cell {
  color: #fff !important;
}
/* 任务区域 */
.gantt_task_bg .gantt_task_row, .gantt_row {
  border-bottom: 1px solid rgba(0, 0 ,0, .05) !important;
}
.gantt_task_bg .gantt_task_row, .gantt_task_cell {
  border-right: 1px solid rgba(0, 0 ,0, .05) !important;
}
/* 修改选择行背景色 */
.gantt_grid_data .gantt_row.gantt_selected, .gantt_grid_data .gantt_row.odd.gantt_selected,
.gantt_task_row.odd.gantt_selected, .gantt_task_row.gantt_selected {
  background-color: #4a4b53 !important;
}
/* 修改数据行背景色 */
.gantt_grid_data .gantt_row, .gantt_data_area, .gantt_task_row {
  background-color: rgba(0, 0 ,0, .05) !important;
}
.gantt_add:before, .gantt_grid_head_add:before {
  color: #fff !important;
}
/* 任务条整体（未完成部分） */
/* .gantt_task_line {
  background-color: red;
} */
.gantt_bar_task {
  background-color: red;
}
/* 任务进度条（已完成部分） */
.gantt_task_progress {
  background-color: rgba(0, 0 ,0, .05) !important;
}
.gantt-marker .today {
  background: #ff0000 !important; /* 红色背景 */
  width: 2px !important;
  z-index: 1 !important;
}
</style>
