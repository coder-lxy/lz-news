<template>
  <div class="pub_news">
    <PubHeader
      class="header"
      :title="editArticle.title"
      @pubClick="handleClick"
    ></PubHeader>
    <MavonEditor :content="editArticle.article" @getContent="setContent" />
    <el-dialog
      title="发布文章"
      :visible.sync="dialogVisible"
      width="40%"
      :before-close="handleClose"
    >
      <PubSelect
        :editArticle="editArticle"
        @hiddenDialog="handleHid"
      ></PubSelect>
    </el-dialog>
  </div>
</template>
<script>
import PubHeader from './components/PubHeader'
import PubSelect from './components/PubSelect'
import MavonEditor from './components/MavonEditor'
import { getNews } from '@/services/newsService'
import TurndownService from 'turndown'
export default {
  data() {
    return {
      editArticle: {
        title: '',
        summary: '',
        article: '',
        labels: [],
        types: [],
      },
      dialogVisible: false,
    }
  },
  components: {
    PubHeader,
    PubSelect,
    MavonEditor,
  },
  created() {
    getNews(this.$route.query.id).then((v) => {
      this.editArticle = v.data
      console.log(this.editArticle)
      const turndown = new TurndownService({
        emDelimiter: '_',
        linkStyle: 'inlined',
        headingStyle: 'atx',
      })
      this.editArticle.article = turndown.turndown(this.editArticle.article)
    })
  },
  methods: {
    handleClick(val) {
      this.editArticle.title = val
      this.dialogVisible = true
    },
    setContent(content) {
      this.editArticle.article = content
    },
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then((_) => {
          done()
        })
        .catch((_) => {})
    },
    handleHid(val) {
      this.dialogVisible = val
    },
  },
}
</script>

<style scoped>
.pub_news .header {
  margin: 10px 20px;
}
</style>
