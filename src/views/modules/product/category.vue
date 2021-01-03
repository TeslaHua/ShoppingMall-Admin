<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    @node-click="handleNodeClick"
  ></el-tree>
</template>

<script>
export default {
  data() {
    return {
      data: [],
      defaultProps: {
        children: "children",
        label: "label",
      },
    };
  },
  methods: {
    handleNodeClick(data) {
      console.log(data);
    },
    //发起请求获取菜单数据
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get",
        // then 表示发送成功了
      }).then(({ data }) => {
        console.log("成功获取到菜单数据...",data)
      });
    },
  },
  //生命周期 - 创建完成 (当作访问当前 this 实例)
  created(){
    this.getMenus();   //一创建这个 tree 组件就给后端发送请求
  }
};
</script>

<style>
</style>