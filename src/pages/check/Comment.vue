<template>
  <div class="commont">
    <!-- 表格 -->
    <el-table :data="commont">
      <el-table-column prop="id" label="编号" width="80" />
      <el-table-column prop="score" label="分数" />
      <el-table-column prop="content" label="评论内容" />
      <el-table-column prop="commentTime" label="评论时间" />
      <el-table-column prop="orderId" label="订单序号" />
      <el-table-column label="操作" fixed="right">
        <template slot-scope="scope">
          <el-button type="primary" size="small" @click="pinglun(scope.row)">审核</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="评论审核" :visible.sync="visible">
      <p>订单序号：{{ this.sh.orderId }}</p>
      <p>分数：{{ this.sh.score }}</p>
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
      commont: [],
      sh: []
    }
  },
  created() {
    this.loadCommint()
  },
  methods: {
    loadCommint() {
      const url = this.baseUrl + '/comment/findAll'
      get(url).then(response => {
        this.commont = response.data
      })
    },
    pinglun(row) {
      this.sh = row
      this.visible = true
    },
    submit() {
      const url = this.baseUrl + '/comment/auditing'
      get(url, { id: this.sh.id }).then(response => {
        this.$message({ type: 'success', message: response.message })
        this.loadCommint()
      })
      this.visible = false
    },
    refuse() {
      const url = this.baseUrl + '/comment/refuseauditing'
      get(url, { id: this.sh.id }).then(response => {
        this.$message({ type: 'success', message: response.message })
        this.loadCommint()
      })
      this.visible = false
    }
  }
}
</script>
