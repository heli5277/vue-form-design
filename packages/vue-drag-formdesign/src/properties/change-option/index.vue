<!--
 修改多选、下拉、单选等控件options的组件，添加移除校验规制的组件
-->

<template>
  <div class="option-change-container">
   
    <el-row  
      v-if="[ 
        'radio',
        'checkbox',
        'select'
        ].includes(type)" :gutter="8">
      <div class="option-change-box" v-for="(val, index) in value" :key="index">
        <el-col :span="9"
          ><el-input v-model="val.label" placeholder="名称"
        /></el-col>
        <el-col :span="9"><el-input v-model="val.value" placeholder="值"/></el-col>
        <el-col :span="6"
          ><div @click="handleDelete(index)" class="option-delete-box">
            <i class="el-icon-delete" /></div
        ></el-col>
      </div>
      <el-col :span="24"><el-button type="primary" @click="handleAdd">添加</el-button></el-col>
    </el-row>
    <!-- 级联 树状数据 lyf 2020-07-06 -->
    <el-row v-if="type === 'cascader'" :gutter="8">
      <div  >
        <el-tree
          :data="value"
          show-checkbox
          node-key="value"
          default-expand-all
          :expand-on-click-node="false">
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <span> 
              <el-row :gutter="4">
                <el-col :span="9">
                  <el-input v-model="data.label" placeholder="名称" />
                </el-col>
                <el-col :span="9">
                  <el-input v-model="data.value" placeholder="值"/>
                </el-col>
                <el-col :span="6">
                  <el-button
                    type="text"
                    size="mini"
                    @click="() => append(data)">
                    <i class="el-icon-circle-plus-outline"></i>
                  </el-button>
                  <el-button
                    type="text"
                    size="mini"
                    @click="() => remove(node, data)">
                    <i class="el-icon-delete"></i>
                  </el-button>
                </el-col>
              </el-row>
            </span>
            
          </span>
        </el-tree>
      </div>
      <el-col :span="24"><el-button type="primary" @click="handleAdd">添加</el-button></el-col>
    </el-row>

    <el-row v-if="type === 'rules'" :gutter="8">
      <span v-for="(val, index) in value" :key="index">
        <div class="option-change-box" v-if="index !== 0">
          <el-col :span="18"
            ><el-input v-model="val.message" placeholder="提示信息"
          /></el-col>
          <el-col :span="18"
            ><el-input v-model="val.pattern" placeholder="正则表达式pattern"
          /></el-col>
          <el-col :span="6"
            ><div @click="handleDelete(index)" class="option-delete-box">
              <i class="el-icon-delete" /></div
          ></el-col>
        </div>
      </span>
      <el-col :span="24"><el-button type="primary" @click="handleAddRules">增加校验</el-button></el-col>
    </el-row>
    <el-row v-else-if="type === 'colspan'" :gutter="8">
      <div class="option-change-box" v-for="(val, index) in value" :key="index">
        <el-col :span="18"
          ><el-input-number
            style="width:100%"
            :max="24"
            v-model="val.span"
            placeholder="名称"
        /></el-col>
        <el-col :span="6"
          ><div @click="handleDelete(index)" class="option-delete-box">
             <i class="el-icon-delete" /></div
        ></el-col>
      </div>
      <el-col :span="24"><el-button type="primary" @click="handleAddCol">添加</el-button></el-col>
    </el-row>
  </div>
</template>
<script>
 
export default {
  name: "ChangeOption",
  props: {
    value: {
      type: Array,
      required: true
    },
    type: {
      type: String,
      default: "option"
    }
  },
  methods: {
    handleAdd() {
      // 添加
      let addData = [
        ...this.value,
        {
          value: "",
          label: ""
        }
      ];
      this.$emit("input", addData);
    },
    handleAddCol() {
      // 添加栅格Col
      let addData = [
        ...this.value,
        {
          span: 12,
          list: []
        }
      ];
      this.$emit("input", addData);
    },
    handleAddRules() {
      let addData = [
        ...this.value,
        {
          pattern: "",
          message: ""
        }
      ];
      this.$emit("input", addData);
    },
    handleDelete(deleteIndex) {
      // 删除
      this.$emit(
        "input",
        this.value.filter((val, index) => index !== deleteIndex)
      );
    },
    append(data) {
        const newChild = { value: '', label: '', children: [] };
        if (!data.children) {
          this.$set(data, 'children', []);
        }
        data.children.push(newChild);
    },

    remove(node, data) {
        const parent = node.parent;
        const children = parent.data.children || parent.data;
        const index = children.findIndex(d => d.value === data.value);
        children.splice(index, 1);
    },

  }
};
</script>
<style  >
 .option-change-container {
  width: calc(100% - 8px);
}
.option-change-container .el-tree-node__content {
  height: 35px;
}
.option-change-container .el-tree-node__content .custom-tree-node {
  height: 35px;
}
.option-change-container .option-change-box {
  height: 38px;
  padding-bottom: 6px;
}
.option-change-container .option-change-box .option-delete-box {
  margin-top: 3px;
  background: #ffe9e9;
  color: #f22;
  width: 32px;
  height: 32px;
  line-height: 32px;
  text-align: center;
  border-radius: 50%;
  overflow: hidden;
  transition: all 0.3s;
}
.option-change-container .option-change-box .option-delete-box:hover {
  background: #f22;
  color: #fff;
}




</style>
