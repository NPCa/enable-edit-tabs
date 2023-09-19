import { createData } from '@/api/data';
<template>
  <div v-show="show">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: 'TabPane',
  props: {
    label: {
      type: [String, Number]
    },
    name: {
      type: [String, Number]
    },
    edit: {
      type: [Boolean]
    }
  },
  data() {
    return {
      show: true
    }
  },
  methods: {
    updateNav() {
      this.$parent.updateNav()
      // this.TabsInstance.updateNav()
    }
  },
  create() {},
  mounted() {
    this.updateNav()
  },
  watch: {
    label() {
      this.updateNav()
    }
  }
}
</script>
<style scoped>
.tab-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.tab-list {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  border: 1px solid #ccc;
  padding: 1rem;
}

.tab {
  cursor: pointer;
  border: none;
  outline: none;
  font-size: 1rem;
  transition: background-color 0.3s ease;
}

.tab.is-active {
  background-color: #ccc;
}

.tab-content-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.tab-content {
  border: 1px solid #ccc;
  padding: 1rem;
}
</style>
