<template>
  <div class="app-container">
    <span class="header-title">错题分类</span>
    <div class="crumbs disNone">
      <el-breadcrumb separator="/">
          <el-breadcrumb-item><i class="el-icon-date"></i> 错题本</el-breadcrumb-item>
          <el-breadcrumb-item>错题分类</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="big-box1200">
      <div class="list-gbtn">
        <div></div>
        <div>
          <el-button icon="el-icon-refresh" circle @click="getCategory"></el-button>
          <el-button type="info" icon="el-icon-edit" @click="showEdit=!showEdit" circle></el-button>
          <el-button type="primary" icon="el-icon-plus" @click="showAdd=!showAdd" circle></el-button>
          <el-button type="danger" icon="el-icon-delete" circle @click="showDelete=!showDelete"></el-button>
        </div>
      </div>
      <div class="container">
        <div v-if="showLoading" class="loading-box">
          <i class="el-icon-loading"></i>
          加载中...
        </div>
        <div v-if="!types[0] && !showLoading" class="loading-box">
          <i class="el-icon-search"></i>
          空空如也...
        </div>
        <el-row :gutter="15">
          <el-col  :xs="12" :sm="12" :md="8" :lg="8" :xl="8" v-for="(item,index) in types" :key="index">
              <el-card>
                <i v-if="showEdit" class="el-icon-edit icon-update" @click="showEditBox(index)"></i>
                <i v-if="showDelete" class="el-icon-close icon-delete" @click="toDetele(item.Id)"></i>
                <div class="big-box">
                  <div class="big-body">
                    <b class="type_title">{{item.Name}}</b>
                    <div><p class="tipbox type_content" v-html="item.Intro"></p></div>
                  </div>
                </div>
              </el-card>
          </el-col>
        </el-row>
      </div>
    </div>
    <el-dialog title="添加错题分类" :visible.sync="showAdd">
      <el-form :model="toadd" :rules="rules" ref="Form" label-width="100px">
        <el-form-item label="错题分类名" prop="Name">
          <el-input v-model="toadd.Name"></el-input>
        </el-form-item>
        <el-form-item label="分类说明" prop="Intro">
          <el-input type="textarea" v-model="toadd.Intro"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showAdd = false">取 消</el-button>
        <el-button type="primary" @click="addMistakeType">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="修改错题分类" :visible.sync="showUpdate">
      <el-form :model="toupdate" :rules="rules" ref="updateForm" label-width="100px">
        <el-form-item label="错题分类名" prop="Name">
          <el-input v-model="toupdate.Name"></el-input>
        </el-form-item>
        <el-form-item label="分类说明" prop="Intro">
          <el-input type="textarea" v-model="toupdate.Intro"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showUpdate = false">取 消</el-button>
        <el-button type="primary" @click="updateMistakeCate">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>

import {
  mistakeCate,
  AddMistakeCate,
  DeleteMistakeCate,
  UpdateMistakeCate
} from '@/api/toPost'

import qs from 'qs'

export default {
  name: 'mistake_type',
  data() {
    return {
      homeTop: 0,
      showLoading: true,
      showDelete: false,
      showAdd: false,
      showEdit: false,
      showUpdate: false,
      types: [],
      toadd: {
        UserId: this.$store.getters.user.Id,
        Name: '',
        Intro: ''
      },
      toupdate: {
        UserId: this.$store.getters.user.Id,
        MistakeCateId: '',
        Name: '',
        Intro: ''
      },
      rules: {
        Name: [
          { required: true, message: '请输入分类名', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ],
        Intro: [
          { min: 0, max: 30, message: '长度在 0 到 30 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  created() {
    this.getCategory()
  },
  // activated() {
  //   document.querySelector('.app-main').scrollTop = this.homeTop || 0
  //   this.getCategory()
  // },
  methods: {
    getCategory() {
      this.showLoading = true
      mistakeCate(qs.stringify({ UserId: this.$store.getters.user.Id })).then(res => {
        if (res.data.code === 0) {
          this.types = res.data.data
        } else {
          this.$message.warning('获取错题分类失败...')
        }
        this.showLoading = false
      }).catch(() => {})
    },
    addMistakeType() {
      this.$refs.Form.validate(valid => {
        if (valid) {
          AddMistakeCate(qs.stringify(this.toadd)).then(res => {
            if (res.data.code === 0) {
              this.$notify({
                title: '提示',
                message: '添加分类成功！',
                type: 'success'
              })
            } else {
              this.$message.warning('操作失败...')
            }
            this.showAdd = false
            this.getCategory()
          }).catch(() => {})
        }
      })
    },
    showEditBox(index) {
      this.toupdate.MistakeCateId = this.types[index].Id
      this.toupdate.Name = this.types[index].Name
      this.toupdate.Intro = this.types[index].Intro
      this.showUpdate = true
    },
    updateMistakeCate() {
      this.$refs.updateForm.validate(valid => {
        if (valid) {
          UpdateMistakeCate(qs.stringify(this.toupdate)).then(res => {
            if (res.data.code === 0) {
              this.$notify({
                title: '提示',
                message: '修改分类信息成功！',
                type: 'success'
              })
            } else {
              this.$message.warning('操作失败...')
            }
            this.showUpdate = false
            this.getCategory()
          }).catch(() => {})
        }
      })
    },
    toDetele(index) {
      this.$confirm('此操作将不仅删除该分类,该分类下的错题都将删除，无法还原, 是否继续?', '警告', {
        confirmButtonText: '全都不要了',
        cancelButtonText: '先留着吧',
        type: 'error'
      }).then(() => {
        DeleteMistakeCate(qs.stringify({ UserId: this.$store.getters.user.Id, MistakeCateId: index })).then(res => {
          if (res.data.code === 0) {
            this.$notify({
              title: '提示',
              message: '成功删除分类，并删除了' + res.data.data + '条错题',
              type: 'success'
            })
          } else {
            this.$message.warning('操作失败...')
          }
          this.getCategory()
        }).catch(() => {})
        //
      }).catch(() => {})
    }
  },
  mounted() {
  }
}
</script>

<style scoped lang="scss">
.el-card {
  margin-bottom: 20px;
  position: relative;
  .type_title{
    font-size: 16px;
    margin-bottom: 5px;
    display: block;
    margin: 0 auto 5px;
    text-align: center;
    width: 90%;
  }
  .type_content {
    margin-bottom: 0;
    line-height: 26px;
    font-size: 14px;
    padding: 10px;
    background-color: #e1f3d8;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
    height: 62px;
  }
}
@media (max-width: 768px) {
  .el-card {
    .type_content{
      font-size: 10px;
      padding: 5px;
    }
  }
}
</style>
