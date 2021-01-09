<template>
  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      @node-click="handleNodeClick"
      :expand-on-click-node="false"
      :show-checkbox="true"
      :default-expanded-keys="expandedKeys"
      node-key="catId"
    >
      <!-- render-content 增删分类数据 -->
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level <= 2"
            type="text"
            size="mini"
            @click="() => append(data)"
          >
            Append
          </el-button>
          <el-button
            v-if="node.childNodes.length == 0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >
            Delete
          </el-button>
        </span>
      </span>
    </el-tree>
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
      <el-form :model="category">
        <el-form-item label="分类名称">
          <el-input v-model="category.name" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCategory">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      category: { name: "", parentCid: 0, catLevel: 0, showStatus: 1, sort: 0 },
      dialogVisible: false, //默认关闭对话框
      menus: [],
      expandedKeys: [],
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
        this.menus = data.data;
      });
    },
    // 分类数据的增删方法
    append(data) {
      this.dialogVisible = true; //点击 append 打开对话框
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel * 1 + 1;
    },
    // 添加三级分类的方法
    addCategory() {
      this
        .$http({
          url: this.$http.adornUrl("/product/category/save"),
          method: "post",
          data: this.$http.adornData(this.category, false),
        })
        .then(({ data }) => {
          this.$message({
            message: "菜单保存成功!",
            type: "success",
          });
          //关闭对话框
          this.dialogVisible = false;
          this.getMenus(); //重新刷新页面
          //设置需要默认展开的菜单节点
          this.expandedKeys = [this.category.parentCid];
        });
    },
    remove(node, data) {
      var ids = [data.catId];
      this.$confirm(`是否删除【${data.name}】菜单?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          }).then(({ data }) => {
            this.$message({
              message: "菜单删除成功!",
              type: "success",
            });
            this.getMenus(); //重新刷新页面
            //设置需要默认展开的菜单节点
            this.expandedKeys = [node.parent.data.catId];
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
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