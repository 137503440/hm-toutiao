<template>
  <div class="comment-container">
    <el-card>
      <div slot="header">
        <my-bread>评论管理</my-bread>
      </div>
      <el-table :data="comments">
        <el-table-column label="标题" prop="title"></el-table-column>
        <el-table-column label="总评论数" prop="total_comment_count"></el-table-column>
        <el-table-column label="粉丝评论数" prop="fans_comment_count"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">{{scope.row.comment_status?'正常':'关闭'}}</template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope" width="100px">
            <el-button
              @click="toggleStatus(scope.row)"
              v-if="!scope.row.comment_status"
              type="success"
              size="small"
            >打开评论</el-button>
            <el-button @click="toggleStatus(scope.row)" v-else type="danger" size="small">关闭评论</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        style="margin-top:20px"
        v-if="total > reqParams.per_page"
        background
        layout="prev, pager, next"
        :total="total"
        :page-size="reqParams.per_page"
        :current-page="reqParams.page"
        @current-change="changePager"
      ></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      comments: [],
      reqParams: {
        response_type: 'comment',
        page: 1,
        per_page: 20
      },
      // 文章总条数
      total: 0
    }
  },
  created () {
    this.getComments()
  },
  methods: {
    //  修改状态
    async toggleStatus (row) {
      const {
        data: { data }
      } = await this.$http.put(`comments/status?article_id=${row.id}`, {
        allow_comment: !row.comment_status
      })
      // 提示
      this.$message.success(
        data.allow_comment ? '打开评论成功' : '关闭评论成功'
      )
      // 修改当前行数据状态，数据驱动视图更新
      row.comment_status = data.allow_comment
    },
    // 页码改变事件函数
    changePager (newPage) {
      this.reqParams.page = newPage
      this.getComments()
    },
    // 获取评论列表
    async getComments () {
      const {
        data: { data }
      } = await this.$http.get('articles', { params: this.reqParams })
      this.comments = data.results
      // 文章总条数
      this.total = data.total_count
    }
  }
}
</script>

<style scoped lang='less'></style>
