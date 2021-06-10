<template>
  <div class="product">
    <!-- 按钮 -->
    <div class="btns">
      <el-button size="small" type="primary" @click="toAdd">新增</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table size="small" :data="products">
      <el-table-column label="编号" type="index" :index="1"/>
      <el-table-column label="名称" prop="name"/>
      <el-table-column label="单价" prop="price"/>
      <el-table-column label="介绍" prop="introduce"/>
      <el-table-column label="所属分类" prop="category.name"/>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" type="danger" @click="delete(scope.row)">删除</el-button>
          <el-button size="mini" type="primary" @click="edit(scope.row)">修改</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格 -->
    <!-- 模态框 -->
    <el-dialog title="录入产品信息" :visible.sync="visible" width="30%">
      <!--{{form}}-->
      <el-form label-width="80px">
        <el-form-item label="产品名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目">
          <!--{{categories}}-->
          <el-select v-model="form.categoryId">
            <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="产品单价">
          <el-input v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="产品介绍">
          <el-input v-model="form.introduce" type="textarea"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="visible=false" size="small">取 消</el-button>
        <el-button type="primary" @click="visible=submit" size="small">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->
  </div>
</template>

<script>
import request from '@/utils/request'

export default {
  data() {
    return {
      name: '产品管理',
      visible: false,
      products: [],
      categories: [],
      form: {}
    }
  },
  created() {
    // 调用加载数据方法进行数据加载
    this.loadProducts()
    // 调用查询栏目的方法加载栏目数据
    this.loadCategories()
  },
  methods: {
    // 提交
    submit() {

    },
    toAdd() {
      this.visible = true
    },
    edit(row) {

    },
    delete(row) {

    },
    // 加载产品数据
    loadProducts() {
      // const url = 'http://39.102.43.224:8888/product/findAllWithCategory'
      const url = this.baseUrl + '/product/findAllWithCategory'
      request.get(url).then((response) => {
        this.products = response.data
      })
    },
    // 加载栏目数据
    loadCategories() {
      // const url = 'http://39.102.43.224:8888/category/findAll'
      const url = this.baseUrl + '/category/findAll'
      request.get(url).then((response) => {
        this.categories = response.data
      })
    }
  }
}
</script>

<style scoped>

</style>
