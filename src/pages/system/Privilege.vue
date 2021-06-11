<template>
  <div class="product">
    <!-- 按钮 -->
    <div>
      <el-button size="small" type="primary" @click="toAdd">新增</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table
      v-loading="loading"
      :data="privilegeWithChild"
      element-loading-text="拼命加载中"
      element-loading-spinner="el-icon-loading"
      element-loading-background="rgba(0, 0, 0, 0.8)"
      row-key="id"
      :tree-pops="{children: 'children', hasChildren: 'hasChildren'}"
    >
      <el-table-column label="编号" type="index" align="center" :index="1"/>

      <el-table-column label="权限名" prop="name" width="300"/>
      <el-table-column label="权限类型" prop="type" width="300"/>

      <el-table-column label="操作" width="280">
        <template slot-scope="scope">
          <el-button type="text" @click="edit(scope.row)">编辑</el-button>
          <el-button type="text" @click="del(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 模态框 -->
    <el-dialog :visible.sync="dialogFormVisible" width="50%" @close="formData={}">
      <!-- 标题内容 -->
      <div slot="title" class="dialog-title">
        <h3>{{ dialogTitle }}</h3>
      </div>
      <!-- {{formData}} -->
      <el-form :mode="formData" label-width="120px">
        <el-form-item label="权限名称：">
          <el-input v-model="formData.name" autocomplete="off" clearable/>
        </el-form-item>
        <el-form-item label="权限类型：">
          <el-input v-model="formData.type" autocomplete="off" clearable/>
        </el-form-item>
        <el-form-item label="所属权限：">
          <el-cascader
            v-model="formData.parentId"
            placeholder="选择权限"
            :options="privileges"
            :props="{ label:'name',value:'id',checkStrictly: true,emitPath:false }"
            clearable
          />
          <!-- <el-select v-model="formData.parentId" placeholder="选择所属栏目">
            <el-option v-for="c in privileges" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>-->
        </el-form-item>
      </el-form>
      <!-- 底部内容 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="text" @click="submit">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { get, post } from '@/utils/request'

export default {
  data() {
    return {
      name: '权限管理',
      value: 1,
      privileges: [], // 初始化，权限数据
      privilegeWithChild: [],
      dialogTitle: '添加新的权限', // 模态框初始标题
      dialogFormVisible: false, // 模态框初始状态(不显示)
      formData: {}, // 模态框数据
      loading: true // 加载表格数据动画的初始状态
      // categorys:[],                    // 栏目列表
    }
  },
  // 表示vue实例创建完毕，开始进行数据渲染
  created() {
    // 加载权限信息
    this.loadPrivilegeWithChild()
    this.loadPrivilege()
  },

  methods: {
    // 加载权限信息
    loadPrivilegeWithChild() {
      const url = this.baseUrl + '/privilege/findAllWithChild'
      get(url).then(res => {
        if (res.status == 200) {
          // 为页面数据赋值
          this.privilegeWithChild = res.data
          // 关闭加载动画
          this.loading = false
        } else {
          this.$message.error(res.message)
        }
      })
    },

    // 加载所有级联栏目
    loadPrivilege() {
      const url = this.baseUrl + '/privilege/findAll'
      get(url).then(response => {
        if (response.status == 200) {
          // 为页面数据赋值
          this.privileges = response.data
        } else {
          this.$message.error(res.message)
        }
      })
    },

    // 提交表单
    submit() {
      const url = this.baseUrl + '/privilege/saveOrUpdate'
      // 获取表单数据
      const data = this.formData
      // 提交
      post(url, data).then((res) => {
        if (res.status == 200) {
          // 提示信息
          this.$message({
            message: res.message,
            type: 'success'
          })
          // 关闭模态框
          this.dialogFormVisible = false
          //  加载权限信息
          this.loadPrivilege()
          // 清空模态框数据
          this.formData = {}
        } else {
          this.$message.error(res.message)
        }
        this.loadPrivilege()
        this.loadPrivilegeWithChild()
      })
    },

    // 点击添加新的权限
    toAdd() {
      // 设置模态框标题
      this.dialogTitle = '添加新的权限'
      // 清空模态框数据
      this.formData = {}
      // 设置模态框可见
      this.dialogFormVisible = true
      this.loadPrivilege()
      this.loadPrivilegeWithChild()
    },

    // 修改编辑
    edit(p) {
      // 设置模态框标题
      this.dialogTitle = '编辑权限'
      // 给模态框数据赋值
      this.formData = Object.assign({}, p)
      // 设置模态框可见
      this.dialogFormVisible = true
    },

    // 删除
    del(pid) {
      this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 删除
        const url = this.baseUrl + '/privilege/deleteById'
        const data = { id: pid }
        get(url, data).then(res => {
          // 提示
          this.$message({ type: 'success', message: res.message })
          // 刷新数据
          this.loadPrivilege()
          this.loadPrivilegeWithChild()
        })
      })
    },

    // 上传成功
    uploadSuccessHandler(res) {
      // console.log(res);
      const photo = res.data.groupname + '/' + res.data.id
      // 下面两个等价
      // this.formData.photo = photo;
      // 拿出原有数据，添加新的重新复制。formData的引用地址发生改变
      this.formData = { ...this.formData, photo }
    },

    // 上传失败
    uploadErrorHandler(error) {
      this.$message({ type: 'error', message: '上传失败' })
    }
  }
}
</script>
