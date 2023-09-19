<template>
  <div class="home">
    <editTabs
      ref="tabs"
      :value="activeTab"
      :edit="isEdit"
      @tab-click="changeTab"
      @save-tab="editTab"
      @delete-tab="deleteTab"
    >
      <template>
        <Tab
          :key="item.value"
          v-for="item in typeList"
          :label="item.name"
          :name="item.id"
        ></Tab>
      </template>
    </editTabs>
  </div>
</template>

<script>
import editTabs from './component/editTab/editTabs.vue'
import Tab from './component/editTab/Tab.vue'
export default {
  name: 'HomeView',
  components: { editTabs, Tab },
  data(){
    return{
      activeTab:'1'
    }
  },
  methods:{
    changeTab(tabName) {
      this.activeTab = tabName
    },
    editTab(tab, index) {
      if (typeof tab.name === 'number') {
        update({
          label: tab.label,
          name: tab.name
        }).then((res) => {
          this.$refs.tabs.updateNav()//需要手动刷新
        })
      } else {
        save({ label: tab.label }).then((res) => {
          this.$refs.tabs.updateNav()
        })
      }
    },
    deleteTab(item, index) {
      if (this.typeList.length === 1) {
        this.$notify.warning('至少保留一个标签!')
        return false
      }
      this.$confirm('删除后将清空该模式下的所有数据，是否确认删除?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          await delete({ name: item.name }).then((res) => {
            res.code === 200
              ? (async () => {
                  this.$notify.success('删除成功')
                  this.$refs.tabs.navList.splice(index, 1)
                  this.activeTab = this.$refs.tabs.navList[index - 1].name
                  this.getTypeList(index - 1)
                })()
              : this.$notify.error(res.message)
          })
        })
        .catch(() => {})
    },
  }
}
</script>
