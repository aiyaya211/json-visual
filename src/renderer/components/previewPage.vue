<template>
    <div>
        <el-button type="primary" @click="back" size="small">返回</el-button>
        预览界面 
    </div>
</template>
<script>
export default {
  data () {
    return {}
  },
  methods: {
    back () {
      this.$router.push({
        path: '/'
      })
    }
  }
}
</script>
<style lang="scss">
</style>