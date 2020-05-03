<template>
  <div class="home">
    <SearchBar
      disabled
      @onClick="onSearchBarClick"
    />
    <HomeCard />
    <HomeBanner
      img="http://www.youbaobao.xyz/book/res/bg.jpg"
      title="mpvue2.0实战多端小程序上线啦"
      subTitle="立即体验"
      @click="onBannerClick"
    />
  </div>
</template>

<script>
import SearchBar from '../../components/home/SearchBar'
import HomeCard from '../../components/home/HomeCard'
import HomeBanner from '../../components/home/HomeBanner'

export default {
  components: {
    SearchBar,
    HomeCard,
    HomeBanner
  },
  methods: {
    onSearchBarClick() {},
    onBannerClick() {}
  }
}
</script>

<style lang="scss" scoped>
</style>
