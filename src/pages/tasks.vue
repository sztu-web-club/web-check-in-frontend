<!-- 打卡任务 -->
<script setup lang="ts">
const tasks = ref<Array<any>>([])
const finish = ref('未打卡')
const userId = ref(1)

const getData = () => {
  uni.request({
    url: `api/v1/user/${userId.value}/tasks`,
    method: 'GET',
    success: (response) => {
      if (Array.isArray(response.data)) {
        tasks.value = response.data
        // 检查是否有任务已完成
        finish.value = response.data.some(task => task.isComplete) ? '已打卡' : '未打卡'
      }
      else {
        console.error('响应数据不是数组')
      }
    },
    fail: (error) => {
      console.error('网络请求失败', error)
      uni.showToast({
        title: '网络请求失败',
        icon: 'none',
      })
    },
  })
}
onMounted(() => {
  getData()
})

const _goDetail = (item: { taskId: number }) => {
  uni.navigateTo({
    url: `/pages/detail?taskId=${item.taskId}`,
  })
}

const checkIn = (tasknum: number) => {
  uni.request({
    url: `api/v1/user/${userId.value}/tasks/${tasknum}/checkin`,
    method: 'POST',
    success: (response) => {
      if (Array.isArray(response.data)) {
        uni.showToast({
          title: '打卡成功',
          icon: 'success',
        })
      }
      else {
        uni.showToast({
          title: '打卡失败',
          icon: 'none',
        })
      }
    },
    fail: (err) => {
      console.error('网络请求失败', err)
      uni.showToast({
        title: '网络请求失败',
        icon: 'none',
      })
    },
  })
}
</script>

<template>
  <view>
    <view class="defaultTop">
      <view class="fontPosition">
        打卡任务
      </view>
    </view>
  </view>

  <view class="task">
    <view class="createTask">
      <navigator
        url="/pages/create"
        open-type="navigate"
        hover-class="navigator-hover"
      >
        <button size="default">
          新建打卡任务
        </button>
      </navigator>
    </view>

    <scroll-view scroll-y class="list">
      <newbox
        v-for="item in tasks"
        :key="item.taskId"
        class="item"
        @click="checkIn(item.taskId)"
      >
        <text class="title">
          {{ item.taskName }}
        </text>
        <text class="isPass">
          {{ finish }}
        </text>
        <text class="pos">
          {{ item.description }}
        </text>
        <view class="status">
          <text class="isPass">
            已签人数：{{ '100' }}
          </text>
          &nbsp;/&nbsp;
          <text class="sum">
            总人数：{{ '200' }}
          </text>
        </view>
      </newbox>
    </scroll-view>
  </view>
</template>

<style scoped>
.view {
  position: relative;
  overflow: hidden;
  box-sizing: border-box;
}

.defaultTop {
  height: 130rpx;
  width: 100%;
  background-color: rgb(78, 199, 106);
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  z-index: 999;
  font-size: 32rpx;
}

.defaultTop .fontPosition {
  color: #fff;
  position: absolute;
  top: 60rpx;
}

/* task */
.task {
  position: relative;
  top: 130rpx;
  color: rgb(182, 182, 182);
  text-align: center;
  font-size: 36rpx;
}

/* createTask */

/* list */
.list {
  margin-bottom: 50rpx;
  width: 100%;
  background-color: #fff;
  color: rgb(163, 163, 163);
  font-size: 24rpx;
}
.list .item {
  padding: 18rpx 22rpx;
  height: 200rpx;
  border-bottom: 1px solid rgb(231, 231, 231);
  display: flex;
  flex-direction: column;
  text-align: left;
}
.list .item .title {
  color: rgb(122, 122, 122);
  font-size: 36rpx;
  font-weight: 700;
  flex: 1;
}
.list .item .ispass,
.list .item .pos,
.list .item .status {
  flex: 1;
}
</style>

<route  lang="json">
{
  "layout": "default"
}
</route>
