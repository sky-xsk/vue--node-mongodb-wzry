<template>
  <div>
    <!-- 导航区 -->
    <Breadcrumb :breadcrumbItem="breadcrumbItem"></Breadcrumb>
    <!-- 内容区 -->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="7">
          <el-input placeholder="请输入内容" clearable v-model="query" @clear="fetch()">
            <el-button slot="append" icon="el-icon-search" @click="fetch()"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click='$router.push("/heroes/create")'>
            <i class="el-icon-plus"></i>添加</el-button>
        </el-col>
      </el-row>
      <el-table :data="heroes" border stripe height="550">
        <el-table-column type="index" width="50"></el-table-column>
        <el-table-column prop="_id" label="ID" width="230"></el-table-column>
        <el-table-column prop="name" label="英雄名称"></el-table-column>
        <el-table-column prop="title" label="英雄称号"></el-table-column>
        <el-table-column prop="avatar" label="英雄头像">
          <template slot-scope="scope">
            <img :src="scope.row.avatar" alt="" style="height:3rem;">
          </template>
        </el-table-column>
        <el-table-column fixed="right" label="操作" width="180">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit" size="small" circle
              @click="$router.push(`/heroes/edit/${scope.row._id}`)"></el-button>
            <el-button type="danger" icon="el-icon-delete" size="small" circle @click="remove(scope.row)"></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区 -->
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNum"
        :page-sizes="[10, 15, 20 , 25,]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper"
        :total="total" background>
      </el-pagination>
    </el-card>
  </div>
</template>
<script>
  import Breadcrumb from '../components/Breadcrumb'
  export default {
    components: {
      Breadcrumb
    },
    data() {
      return {
        heroes: [],
        breadcrumbItem: ['运营管理', '英雄管理'],
        total: 0,
        query: '',
        pageNum: 1, // 当前页
        pageSize: 10, // 页大小
      }
    },
    methods: {
      // 获取列表
      async fetch() {
        const res = await this.$http.get(
          `rest/heroes?pageNum=${this.pageNum}&pageSize=${this.pageSize}&query=${this.query}`);
        this.heroes = res.data.items;
        this.total = res.data.count;
      },
      // 删除
      async remove(row) {
        this.$confirm(`是否删除英雄"${row.name}"`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          await this.$http.delete(`rest/heroes/${row._id}`)

          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.fetch(); // 刷新列表
        }).catch(() => {

        });
      },
      // 监听页码值的改变
      handleCurrentChange(newPage) {
        this.pageNum = newPage
        this.fetch()
      },
      // 监听页码大小
      handleSizeChange(newSize) {
        this.pageSize = newSize
        this.fetch()
      }
    },
    created() {
      this.fetch(); // 在列表组件渲染成功后自动执行该方法获取数据库数据
    }
  }
</script>
<style lang='less' scoped>
</style>