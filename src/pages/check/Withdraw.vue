<template>
  <div class="withdraw">
    <!-- 表格 -->
    <el-table :data="customers">
      <el-table-column prop="id" label="编号" width="150"/>
      <el-table-column prop="realname" label="用户名"/>
      <el-table-column prop="telephone" label="支付宝号"/>
      <el-table-column prop="balance" label="提现金额"/>
      <el-table-column label="操作" fixed="right">
        <template slot-scope="scope">
          <el-button type="primary" size="small" @click="shenhe(scope.row)">审核</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="提现审核" :visible.sync="visible">
      <p>用户名：{{ this.emp.realname }}</p>
      <p>支付宝号：{{ this.emp.telephone }}</p>
      <p>提现金额：{{ this.emp.balance }}</p>

      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submit">审核通过</el-button>
        <el-button type="danger" @click="refuse">拒绝审核</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
import { get } from '@/utils/request'

export default {
  data() {
    return {
      visible: false,
      customers: [],
      emp: []
    }
  },
  created() {
    this.loadCustomers()
  },
  methods: {
    loadCustomers() {
      const url = this.baseUrl + '/user/findAll'
      get(url).then(response => {
        this.customers = response.data
      })
    },
    shenhe(row) {
      this.emp = row
      this.visible = true
    },
    submit() {
      const url = this.baseUrl + '/user/deleteById'
      get(url, { id: this.emp.id }).then(response => {
        this.$message({ type: 'success', message: response.message })
        this.loadCustomers()
      })
      this.visible = false
    },
    refuse() {
      const url = this.baseUrl + '/user/refuseauditing1'
      get(url, { id: this.emp.id }).then(response => {
        this.$message({ type: 'success', message: response.message })
        this.loadCustomers()
      })
      this.visible = false
    }
  }
}
</script>
