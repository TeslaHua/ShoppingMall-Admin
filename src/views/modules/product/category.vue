<template>
  <el-tree :data="menus" :props="defaultProps" @node-click="handleNodeClick" :expand-on-click-node = 'false' show-checkbox="true">
    <!-- render-content 增删分类数据 -->
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button v-if="node.level <= 2" type="text" size="mini" @click="() => append(data)">
          Append
        </el-button>
        <el-button v-if="node.childNodes.length==0" type="text" size="mini" @click="() => remove(node, data)">
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      defaultProps: {
        children: "children",
        label: "name", //具体要显示的字段
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
        console.log("成功获取到菜单数据...", data);
        this.menus = data.data;
      });
    },
    // 分类数据的增删方法
    append(data) {
        console.log(data)
    },
    remove(node, data) {
        console.log(node,data)
    },
  },
  //生命周期 - 创建完成 (当作访问当前 this 实例)
  created() {
    this.getMenus(); //一创建这个 tree 组件就给后端发送请求
  },
};
</script>

<style>
</style>