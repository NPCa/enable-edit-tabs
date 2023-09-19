<template>
  <div style="display: flex">
    <div class="edit-tab-bar-scroll" ref="navScroll">
      <div class="edit-tab">
        <div class="edit-tab-bar">
          <div
            :class="tabCls(item)"
            v-for="(item, index) in navList"
            :key="item.name || index"
            @click="handleChange(item)"
            @dblclick="handDbClick(item)"
          >
            <el-input
              v-model="item.label"
              v-show="item.edit"
              size="mini"
              ref="input"
              v-focus
              placeholder="请输入班组模式"
              @keyup.enter.native="handleConfirm(item, index)"
            />
            <!-- {{item.label}} -->
            <span v-show="!item.edit">
              {{ item.label }}
            </span>
            <i class="el-icon-close" v-show="!item.edit" @click.stop="handleDelete(item, index)"
              >x</i
            >
          </div>
        </div>
      </div>
      <div class="edit-tab-content">
        <slot></slot>
      </div>
    </div>
    <el-button
      style="margin-left: 10px; height: 30px; width: 30px; margin-top: 6px"
      type="primary"
      icon="el-icon-plus"
      size="mini"
      @click="addItem"
      circle
    ></el-button>
  </div>
</template>
<script>
import { findComponentsDownward } from './utils'
import { MessageBox } from 'element-ui'
export default {
  name: 'editTab',
  props: {
    value: {
      type: [String, Number]
    }
  },
  watch: {
    value(val) {
      this.currentValue = val
    },
    currentValue() {
      this.updateStatus()
    }
  },
  data() {
    return {
      navList: [], // 存放子组件Tab的label和name
      currentValue: this.value, // 当前默认选择tab
      navStyle: {
        transform: 'translateX(0px)'
      }
    }
  },
  methods: {
    //获取子组件下tab的数量
    getTabs() {
      const allTabPanes = findComponentsDownward(this, 'TabPane') // 找到下方name为tabpane组件
      return allTabPanes
    },
    // 设置tabitem样式
    tabCls(item) {
      return [
        'edit-tab-item',
        {
          ['edit-tab-item-active']: item.name === this.currentValue
        }
      ]
    },
    // 更新头部label和name
    updateNav() {
      this.navList = []
      this.getTabs().forEach((pane, index) => {
        if (!pane.name) {
          // 没有name的容错判断
          pane.name = index
        }
        if (index === 0) {
          if (!this.currentValue) {
            this.currentValue = pane.name || index
          }
        }
        this.navList.push({
          label: pane.label,
          name: pane.name || index,
          edit: pane.edit
        })
      })
      this.updateStatus()
    },
    // 控制tab内容是否显示
    updateStatus() {
      const tabs = this.getTabs()
      tabs.forEach((tab) => (tab.show = tab.name === this.currentValue))
    },
    // 单击选中
    handleChange(item) {
      var that = this
      // 开启延时器，300ms的间隔区分单击和双击，解决双击时执行两次单击事件
      clearTimeout(that.time)
      that.time = setTimeout(() => {
        const nav = this.navList.find((v) => v.name === item.name)
        this.currentValue = nav.name
        if (!item.edit) {
          this.$emit('tab-click', nav.name)
        }
      }, 300)
    },
    // 双击
    handDbClick(item) {
      clearTimeout(this.time)
      if (this.navList.some((item) => item.edit)) {
        this.$message({
          message: '请先确认未保存的班组模式！',
          type: 'warning'
        })
      } else {
        this.$set(item, 'edit', true)
      }
    },
    // 回车
    handleConfirm(item, index) {
      if (item.label == '') {
        this.$notify.error('请输入标签名')
        return
      }
      this.$emit('save-tab', item, index)
      this.$set(item, 'edit', false)
    },
    handleDelete(item, index) {
      this.$emit('delete-tab', item, index)
    },
    addItem() {
      this.navList.push({
        label: '',
        name: 'add',
        edit: true
      })
      this.currentValue = this.navList.length - 1
    }
  }
}
</script>
<style lang="scss" scope>
.edit-tab {
  width: 100%;
  position: relative;
}
.edit-tab-wrapper32 {
  padding: 0 32px;
}
.edit-tab-bar-scroll {
  overflow-y: hidden;
  margin-bottom: 10px;
}
.edit-tab__nav-prev {
  width: 32px;
  text-align: center;
  position: absolute;
  line-height: 44px;
  cursor: pointer;
  left: 0;
}
.edit-tab__nav-next {
  width: 32px;
  text-align: center;
  position: absolute;
  line-height: 44px;
  cursor: pointer;
  right: 0;
}
.edit-tab-bar {
  white-space: nowrap;
  border: 1px solid #dfe4ed;
  border-bottom: none;
  border-radius: 4px 4px 0 0;
  box-sizing: border-box;
  float: left;
}
.edit-tab-item {
  width: auto;
  padding: 0 20px;
  height: 40px;
  box-sizing: border-box;
  line-height: 40px;
  display: inline-block;
  list-style: none;
  font-size: 14px;
  font-weight: 500;
  color: #303133;
  position: relative;
  border-bottom: 1px solid #e4e7ed;
  border-left: 1px solid #e4e7ed;
  transition: color 0.3s cubic-bezier(0.645, 0.045, 0.355, 1),
    padding 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
  cursor: pointer;
  .el-icon-close {
    position: relative;
    font-size: 12px;
    width: 0;
    height: 14px;
    vertical-align: middle;
    line-height: 15px;
    overflow: hidden;
    top: -1px;
    right: -2px;
    transform-origin: 100% 50%;
    border-radius: 50%;
    text-align: center;
    transition: all 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
    margin-left: 5px;
  }
  &:hover {
    .el-icon-close {
      width: 12px;
    }
  }
  &:first-child {
    border-left: none;
  }
  &.edit-tab-item-active {
    border-bottom-color: #fff;
    color: #409eff;
    .el-icon-close {
      width: 12px;
    }
  }
}
.el-icon-close:hover {
  background: #dddddd;
  color: #fff;
}
</style>
