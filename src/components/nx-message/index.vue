<template>
  <div>
    <el-tooltip effect="dark" content="消息" placement="bottom">
      <el-button class=" btn-text can-hover" type="text" @click="dialogVisible = true">
        <el-badge is-dot class="item">
          <i class="header-icon el-icon-bell"></i>
        </el-badge>
       </el-button>
    </el-tooltip>
    <el-dialog title="消息通知" width="800px" :visible.sync="dialogVisible" append-to-body>
    <div class="container mess">
            <el-tabs v-model="message">
                <el-tab-pane name="first">
                  <span slot="label"><el-badge :value="unread.length" class="item"> 未读消息</el-badge></span>
                    <el-table :data="unread" :show-header="false" style="width: 100%">
                        <el-table-column>
                            <template slot-scope="scope">
                                <span class="message-title">{{scope.row.title}}</span>
                            </template>
                        </el-table-column>
                        <el-table-column prop="date" width="100"></el-table-column>
                        <el-table-column fixed="right" width="90">
                            <template slot-scope="scope">
                                <el-button size="small" @click="handleRead(scope.$index)">标为已读</el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                    <div class="handle-row">
                        <el-button type="primary">全部标为已读</el-button>
                    </div>
                </el-tab-pane>
                <el-tab-pane name="second">
                  <span slot="label"><el-badge :value="read.length" class="item"> 已读消息</el-badge></span>
                    <template v-if="message === 'second'">
                        <el-table :data="read" :show-header="false" style="width: 100%">
                            <el-table-column>
                                <template slot-scope="scope">
                                    <span class="message-title">{{scope.row.title}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column prop="date" width="100"></el-table-column>
                            <el-table-column fixed="right" width="70">
                                <template slot-scope="scope">
                                    <el-button size="small" type="danger" @click="handleDel(scope.$index)">删除</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="handle-row">
                            <el-button type="danger">删除全部</el-button>
                        </div>
                    </template>
                </el-tab-pane>
                <el-tab-pane name="third">
                  <span slot="label"><el-badge :value="recycle.length" class="item"> 回收站</el-badge></span>
                    <template v-if="message === 'third'">
                        <el-table :data="recycle" :show-header="false" style="width: 100%">
                            <el-table-column>
                                <template slot-scope="scope">
                                    <span class="message-title">{{scope.row.title}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column prop="date" width="100"></el-table-column>
                            <el-table-column fixed="right" width="70">
                                <template slot-scope="scope">
                                    <el-button size="small" @click="handleRestore(scope.$index)">还原</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="handle-row">
                            <el-button type="danger">清空回收站</el-button>
                        </div>
                    </template>
                </el-tab-pane>
            </el-tabs>
        </div>
    </el-dialog>
  </div>
</template>

<script>
import nxSvgIcon from '@/components/nx-svg-icon/index'
export default {
  name: 'nx-message',
  components: {
    nxSvgIcon
  },
  data() {
    return {
      dialogVisible: false,
      message: 'first',
      showHeader: false,
      unread: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护'
        },
        {
          date: '2018-04-19 21:00:00',
          title: '今晚12点整发大红包，先到先得'
        }
      ],
      read: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护'
        }
      ],
      recycle: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护'
        }
      ]
    }
  },
  methods: {
    handleRead(index) {
      const item = this.unread.splice(index, 1)
      console.log(item)
      this.read = item.concat(this.read)
    },
    handleDel(index) {
      const item = this.read.splice(index, 1)
      this.recycle = item.concat(this.recycle)
    },
    handleRestore(index) {
      const item = this.recycle.splice(index, 1)
      this.read = item.concat(this.read)
    }
  },
  computed: {
    unreadNum() {
      return this.unread.length
    }
  }
}
</script>

<style lang="scss" scoped>
.message-title{
    cursor: pointer;
}
.handle-row{
    margin-top: 30px;
}
.btn-text {
  color: #5a5e66;
  &.can-hover {
    &:hover {
      color: #52bab5;
    }
  }
}
.el-icon-bell{
  font-size: 20px;
  margin-top: -5px;
}
</style>
