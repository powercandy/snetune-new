<template>
  <div class="ad-content-editor">
    <el-form label-width="80px" :rules="rules" :model="formData">
      <el-form-item label="文章标题" prop="title">
        <el-input placeholder="文章标题" v-model="formData.title"></el-input>
      </el-form-item>
      <el-form-item label="文章分类" prop="classify">
        <el-select v-model="formData.classify" placeholder="请选择">
          <el-option
            v-for="item in classifyList"
            :key="item.id"
            :label="item.label"
            :value="item.label">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="状态" porp="status">
        <el-radio-group v-model="formData.status">
          <el-radio :label="0">草稿</el-radio>
          <el-radio :label="1">发布</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="文章内容" prop="content">
        <mavon-editor v-model="formData.markdown" fontSize="12px" @change="markDownChange"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">提交</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 表单数据
      formData: {
        title: '', // 标题
        classify: '', // 分类
        status: 0, // 发布状态
        markdown: '', // 文章markdown内容
        content: '', // 文章html结构
        description: '' // 文章描述
      },
      rules: {
        // 标题规则
        title: [
          { required: true, message: '请输入标题', trigger: 'blur' },
          { min: 3, max: 100, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        tag: [
          { type: 'array', required: true, message: '请至少选择一个标签', trigger: 'change' }
        ]
      },
      // 分类列表
      classifyList: [],
      // 判断页面是新增还是修改
      isUpdateContent: this.$route.params.id === 'add' ? false : this.$route.params.id
    }
  },
  created() {
    this.getClassifyList()
    this.isUpdateContent ? this.getContentData() : ''
  },
  methods: {
    /* 获取分类列表 */
    getClassifyList() {
      this.$api.getMetaList().then(res => {
        // to do 
        if (!res.data.errno) {
          let list = res.data.data.metaInfo
          for (let i = 0; i < list.length; i++) {
            this.classifyList.push({id: i, label: list[i].classify})
          }
        }
      })
    },
    // 获取对应文章内容
    getContentData() {
      let data = {id: this.isUpdateContent}
      this.$api.getContentData(data).then(res => {
        if (!res.data.errno) {
          // to do
          this.formData = Object.assign(this.formData, res.data.data.contentInfo)
        }
      })
    },
    // 新建文章内容
    addContent() {
      this.$api.addContent(this.formData).then(res => {
        if (!res.data.errno) {
          // to do
          this.$message({
            type: 'success',
            message: '添加成功',
            onClose: () => {
              this.$router.push('/content/list')
            }
          })
        }
      })
    },
    // 更新文章内容配置
    updateContent() {
      this.$api.updateContent(this.formData).then(res => {
        if (!res.data.errno) {
          this.$message({
            type: 'success',
            message: '更新成功',
            onClose: () => {
              this.$router.push('/content/list')
            }
          })
        }
      })
    },
    // 监听选择的标签列表
    handleCheckAllChange () {
    },
    // 监听markdown输入
    markDownChange (v, f) {
      this.formData.content = f
      this.formData.description = f
    },
    // 提交信息
    onSubmit () {
      console.log(this.formData)
      this.isUpdateContent ? this.updateContent() : this.addContent()
    }
  }
}
</script>

<style lang="less">
.ad-content-editor {
  background-color: #fff;
  padding: 10px;
  font-size: 12px;
  span {
    font-weight: normal;
  }
  .el-form-item__label {
    font-size: 12px;
    line-height: 32px;
  }
  .el-input__inner {
    height: 32px;
  }
  .el-input__icon {
    line-height: 32px;
  }
  .el-form-item__content {
    line-height: 32px;
  }
  .el-date-editor {
    margin-right: 10px;
  }
  .el-radio__inner {
    height: 12px;
  }
  .el-radio__label {
    height: 12px;
    font-size: 12px;
  }
  
}
body {
  .has-time {
    top: 0!important;
  }
}

</style>
