<template>
  <div>
    <ShelfUserInfo
      :userInfo="userInfo"
      :readDay="readDay"
      :num="shelfList.length - 1"
    />
    <ShelfList
      :shelfList="shelfList"
    />
  </div>
</template>

<script>
import ShelfUserInfo from '../../components/shelf/ShelfUserInfo'
import ShelfList from '../../components/shelf/ShelfList'
import { getStorageSync } from '../../api/wechat'
import { userDay, bookIsInShelf } from '../../api'

export default {
  components: {
    ShelfUserInfo,
    ShelfList
  },
  data() {
    return {
      userInfo: {},
      readDay: 0,
      shelfList: []
    }
  },
  onShow() {
    this.userInfo = getStorageSync('userInfo')
    const openId = getStorageSync('openId')
    userDay({ openId }).then(response => {
      this.readDay = response.data.data.day
    })
    bookIsInShelf({ openId }).then(response => {
      this.shelfList = response.data.data
      this.shelfList.push({})
    })
  }
}
</script>

<style lang="scss" scoped>
</style>
