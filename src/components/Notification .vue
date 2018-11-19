<template>
  <el-button
    plain
    @click="open"
   
  </el-button>
</template>

<script>
  export default {
    props:['title'],
    methods: {
      open() {
        const h = this.$createElement;

        this.$notify({
          title: this.title,
        });
      }
    }
  }
</script>