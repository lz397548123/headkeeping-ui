<template>
  <div class="order">
    <!-- 选项卡 -->
    <el-tabs v-model="param.status">
      <el-tab-pane label="所有订单" name="all" />
      <el-tab-pane label="待支付订单" name="未付款" />
      <el-tab-pane label="待派单订单" name="待派单" />
      <el-tab-pane label="待服务" name="待服务" />
      <el-tab-pane label="待确认" name="待确认" />
      <el-tab-pane label="已完成" name="已完成" />

    </el-tabs>
    <!-- 订单列表 -->
    <el-table :data="orders" size="small">

      <!-- 折叠列 -->
      <el-table-column type="expand">
        <template v-slot="scope">
          <!-- 订单项表格 -->
          <el-table :data="scope.row.orderLines" size="mini" border>
            <el-table-column label="图片" width="80">
              <template v-slot="scope">

                <img
                  style="width:50px; height:50px; border-radius:3px"
                  :src="'http://121.199.29.84:8090/'+scope.row.productPhoto"
                  alt=""
                >

              </template>
            </el-table-column>
            <el-table-column label="名称" prop="productName" width="120" />
            <el-table-column label="单价" prop="productPrice" width="60" />
            <el-table-column label="数量" prop="number" width="120" />
            <el-table-column label="介绍" prop="productIntroduce" />

          </el-table>
          <!-- /订单项表格 -->
        </template>
      </el-table-column>
      <!-- 普通列 -->
      <el-table-column label="编号" type="index" :index="1" align="center" />
      <el-table-column label="下单时间" prop="orderTime" width="150" />
      <el-table-column label="总价" prop="total" width="60" />
      <el-table-column label="顾客" prop="customer.realname" width="60" />
      <el-table-column label="员工" prop="employee.realname" width="60" />
      <el-table-column label="状态" prop="status" width="60" />
      <el-table-column label="地址">

        <template v-slot="scope">
          <div>{{
            scope.row.address.province + ' ' + scope.row.address.city + ' ' + scope.row.address.area + ' ' + scope.row.address.address
          }}
          </div>
          <div>{{ scope.row.address.username }} {{ scope.row.address.telephone }}</div>
        </template>

      </el-table-column>
      <el-table-column label="操作" width="160" align="center">
        <template slot-scope="scope">
          <el-button
            v-if="scope.row.status==='待派单'"
            type="primary"
            size="mini"
            @click="sendOrder(scope.row.id)"
          >去派单
          </el-button>
          <el-button v-if="scope.row.status==='已完成'" disabled type="success" size="mini">已完成</el-button>
          <el-button v-if="scope.row.status==='待服务'" disabled type="primary" size="mini">待服务</el-button>
          <el-button v-if="scope.row.status==='待确认'" disabled type="primary" size="mini">待确认</el-button>

        </template>
      </el-table-column>
    </el-table>
    <!-- 模态框 -->
    <el-dialog title="派单" :visible.sync="visible">
      <el-radio v-for="e in employee" v-if="e.status==='已通过'" :key="e.id" v-model="employeeId" :label="e.id">
        {{ e.realname }}
      </el-radio>

      <el-radio
        v-for="e in employee"
        v-if="e.status==='启用'"
        :key="e.id"
        v-model="employeeId"
        :label="e.id"
      >{{ e.realname }}
      </el-radio>

      <div slot="footer" class="dialog-footer">
        <el-button @click="close()">取消</el-button>
        <el-button type="primary" @click="submit()">确定</el-button>
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
      param: {
        status: 'all'
      },
      orders: [],
      employee: [],
      orderId: '',
      visible: false,
      employeeId: ''
    }
  },
  watch: {
    // 监听param的变化，一旦变化立刻查询
    param: {
      handler: function() {
        this.loadOrders()
      },
      deep: true
    }
  },
  created() {
    // 初始化查询
    this.loadOrders()
  },
  methods: {
    // 加载订单信息
    loadOrders() {
      const url = this.baseUrl + '/order/query'
      // 当状态为all，说明查询所有订单，不对订单状态进行过滤，删除这个属性即可
      // 1. 克隆param
      const param = Object.assign({}, this.param)
      // 2. 处理数据
      if (param.status == 'all') {
        delete param.status
      }
      get(url, param).then((response) => {
        this.orders = response.data
      })
    },
    // 派单
    sendOrder(id) {
      this.orderId = id
      this.visible = true
      this.loadEmployee()
    },
    // 查找所有的员工
    loadEmployee() {
      const url = this.baseUrl + '/user/findAllEmployee'
      get(url).then(response => {
        this.employee = response.data
      })
    },
    close() {
      this.visible = false
    },
    submit() {
      const data = {
        orderId: this.orderId,
        employeeId: this.employeeId
      }

      const url = this.baseUrl + '/order/sendOrder'
      get(url, data).then(response => {
        this.$message({ type: 'success', message: response.message })
      })
      this.visible = false
      this.loadOrders()
    }
  }
}

</script>
