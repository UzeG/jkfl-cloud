<template>
  <div class="card-list" ref="content">
    <a-list :grid="{ gutter: 24, lg: 3, md: 2, sm: 1, xs: 1 }" :dataSource="dataSource">
      <a-list-item slot="renderItem" slot-scope="item">
        <a-card :hoverable="true" @click="joinExam(item.id, item.date, item.elapse)">
          <a-card-meta>
            <div style="margin-bottom: 3px" slot="title">{{ item.title }}</div>
            <div class="meta-content" slot="description">
              <p>{{ item.content }}</p>
              <p>开始时间：{{ item.date }}</p>
            </div>
          </a-card-meta>
          <template class="ant-card-actions" slot="actions">
            <a>满分：{{ item.score }}分</a>
            <a>限时：{{ item.elapse }}分钟</a>
          </template>
        </a-card>
      </a-list-item>
    </a-list>
  </div>
</template>

<script>
import { getExamCardList, getExamRecordList } from '../../api/exam'

export default {
  name: 'ExamCardList',
  data() {
    return {
      description: '您可以随意点击下面的考试卡片开始一场属于您的考试',
      extraImage: 'https://gw.alipayobjects.com/zos/rmsportal/RzwpdLnhmvDJToTdfDPe.png',
      dataSource: [],
      record: {},
    }
  },
  methods: {
    joinExam(id, date, elapse) {
      /* 测试用 */
      // const routeUrl = this.$router.resolve({
      //   path: `/exam/${id}`,
      // })
      // window.open(routeUrl.href, '_blank')

      /* 以下测试时注释 */
      const newDate = new Date()
      if (Date.parse(date) < newDate && Date.parse(date) + elapse * 60 * 1000 > newDate) {
        for (const ex in this.record) {
          if (id === this.record[ex].examId) {
            return this.$notification.error({
              message: '您已参加过本场考试！不可重复参加！',
            })
          } else {
            const routeUrl = this.$router.resolve({
              path: `/exam/${id}`,
            })
            window.open(routeUrl.href, '_blank')
          }
        }
      } else {
        this.$notification.error({
          message: '未在考试时间！进入考试失败！',
        })
      }
    },
  },
  mounted() {
    // 从后端数据获取考试列表，适配前端卡片
    getExamCardList()
      .then((res) => {
        console.log(res)
        if (res.code === 0) {
          this.dataSource = res.data
        } else {
          this.$notification.error({
            message: '获取考试列表失败',
            description: res.msg,
          })
        }
      })
      .catch((err) => {
        // 失败就弹出警告消息
        this.$notification.error({
          message: '获取考试列表失败',
          description: err.message,
        })
      })
    getExamRecordList()
      .then((res) => {
        console.log(res)
        if (res.code === 0) {
          this.record = res.data
        } else {
          this.$notification.error({
            message: '获取考试记录失败',
            description: res.msg,
          })
        }
      })
      .catch((err) => {
        // 失败就弹出警告消息
        this.$notification.error({
          message: '获取考试记录失败',
          description: err.message,
        })
      })
  },
}
</script>

<style lang="less" scoped>
.card-avatar {
  width: 48px;
  height: 48px;
  border-radius: 48px;
}

.ant-card-actions {
  background: #f7f9fa;

  li {
    float: left;
    text-align: center;
    margin: 12px 0;
    color: rgba(0, 0, 0, 0.45);
    width: 50%;

    &:not(:last-child) {
      border-right: 1px solid #e8e8e8;
    }

    a {
      color: rgba(0, 0, 0, 0.45);
      line-height: 22px;
      display: inline-block;
      width: 100%;

      &:hover {
        color: #1890ff;
      }
    }
  }
}

.new-btn {
  background-color: #fff;
  border-radius: 2px;
  width: 100%;
  height: 188px;
}

.meta-content {
  position: relative;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  height: 64px;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
}
</style>
