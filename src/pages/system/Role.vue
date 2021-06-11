<template>
  <div>
    <!-- 选项卡 -->
    <el-tabs v-model="param.roleId">
      <el-tab-pane label="管理员" name="1" />
      <el-tab-pane label="顾客" name="2" />
      <el-tab-pane label="员工" name="3" />
    </el-tabs>

    <!-- 表格 -->
    <el-table
      v-loading="loading"
      :data="roles"
      element-loading-text="拼命加载中"
      element-loading-spinner="el-icon-loading"
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
      <el-table-column prop="id" label="编号" />
      <el-table-column prop="realname" label="姓名" />
      <el-table-column prop="roleId" label="身份类别">
        <template slot-scope="scope">
          <p v-if="scope.row.roleId === 1">管理员</p>
          <p v-if="scope.row.roleId === 2">顾客</p>
          <p v-if="scope.row.roleId === 3">员工</p>
        </template>
      </el-table-column>

      <el-table-column prop="gender" label="性别" />
      <el-table-column prop="status" label="状态" />
      <el-table-column fixed="right" label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="edit(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="角色编辑" :visible.sync="visable">
      <el-form ref="ruleForm" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="姓名">
          <el-input v-model="form.realname" clearable placeholder="请输入真实姓名" />
        </el-form-item>
        <el-form-item label="身份类别" prop="roleId">
          <el-select v-model="form.roleId" placeholder="请选择用户类型" size="small" clearable>
            <el-option
              v-for="item in roleOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="性别" prop="gender">
          <el-select v-model="form.gender" placeholder="请选择性别">
            <el-option label="男" value="男" />
            <el-option label="女" value="女" />
          </el-select>
        </el-form-item>
        <!-- {{status}} -->
        <el-form-item label="状态" prop="status">
          <el-select v-model="form.status" placeholder="请选择状态">
            <el-option label="禁用" value="禁用" />
            <el-option label="启用" value="启用" />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="close('ruleForm')">取消</el-button>
        <el-button type="primary" @click="submit('ruleForm')">确定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框/ -->
  </div>
</template>
<script>
import { get, post } from '@/utils/request'

export default {
  data() {
    return {
      param: {
        roleId: 1
      },
      visable: false,
      loading: true,
      form: {},
      roles: [],
      roleOptions: [
        {
          value: 1,
          label: '管理员'
        },
        {
          value: 2,
          label: '顾客'
        },
        {
          value: 3,
          label: '员工'
        }
      ],
      status: [
        {
          required: true,
          message: '请选择性别',
          trigger: 'change'
        }
      ],
      rules: {
        realname: [
          {
            required: true,
            message: '请输入用户名',
            trigger: 'blur'
          }
        ],
        status: [
          {
            required: true,
            message: '请选择状态',
            trigger: 'change'
          }
        ]
      }
    }
  },
  watch: {
    // 监听param的变化，一旦变化立刻查询
    param: {
      handler: function() {
        this.loadRole()
      },
      deep: true
    }
  },
  created() {
    this.loadRole()
  },
  methods: {
    loadRole() {
      const url = this.baseUrl + '/user/findExample'
      const param = Object.assign({}, this.param)
      post(url, param).then(response => {
        this.roles = response.data
        this.loading = false
      })
    },
    submit(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          // 表单验证成功的情况
          const url = this.baseUrl + '/user/saveOrUpdate'
          post(url, this.form).then(response => {
            this.$message({
              type: 'success',
              message: response.message
            })
            this.visable = false
            this.loadRole()
            this.$refs[form].resetFields()
          })
        } else {
          return false
        }
      })
    },
    close(form) {
      this.visable = false
      this.$refs[form].resetFields()
    },
    edit(row) {
      this.form = row
      this.visable = true
    }
  }
}
</script>

<style>
</style>
