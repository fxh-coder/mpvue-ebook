<template>
  <div>
    <SearchBar
      :focus="searchFocus"
      @onChange="onChange"
      @onClear="onClear"
      :hot-search="hotSearchKeyword"
      @onConfirm="onConfirm"
      ref="searchBar"
    />
    <TagGroup
      :value='hotSearchArray'
      header-text="热门搜索"
      btn-text="换一批"
      @onTagClick="showBookDetail"
      @onBtnClick="changeHotSearch"
      v-if="hotSearchArray.length > 0 && !showList"
    />
    <TagGroup
      :value='historySearch'
      header-text="历史搜索"
      btn-text="清空"
      @onTagClick="searchKeyWord"
      @onBtnClick="clearHistorySearch"
      v-if="historySearch.length > 0 && !showList"
    />
    <SearchList :data="searchList" v-if="showList" />
  </div>
</template>

<script>
import SearchBar from '../../components/home/SearchBar'
import TagGroup from '../../components/base/TagGroup'
import SearchList from '../../components/search/SearchList'
import { getStorageSync, setStorageSync, showToast } from '../../api/wechat'
import { search, hotSearch } from '../../api'

const KEY_HISTORY_SEARCH = 'historySearch'

export default {
  components: {
    SearchBar,
    TagGroup,
    SearchList
  },
  computed: {
    showList() {
      const keys = Object.keys(this.searchList)
      return keys.length > 0
    },
    hotSearchArray() {
      const _hotSearch = []
      this.hotSearch.forEach(o => _hotSearch.push(o.title))
      return _hotSearch
    }
  },
  data() {
    return {
      hotSearch: [],
      historySearch: [],
      searchList: {},
      searchFocus: true,
      openId: '',
      hotSearchKeyword: '',
      page: 1
    }
  },
  methods: {
    onConfirm(keyword) {
      if(!keyword || keyword.trim().length === 0) {
        keyword = this.hotSearchKeyword
        this.$refs.searchBar.setValue(keyword)
      }
      this.onSearch(keyword)
      if(!this.historySearch.includes(keyword)) {
        this.historySearch.push(keyword)
        setStorageSync(KEY_HISTORY_SEARCH, this.historySearch)
      }
      this.searchFocus = false
    },
    onClear() {
      this.searchList = {}
    },
    onChange(keyword) {
      if(!keyword || keyword.trim().length === 0) {
        this.searchList = {}
        return
      }
      this.page = 1
      this.onSearch(keyword)
    },
    onSearch(keyword) {
      search({
        keyword,
        openId: this.openId,
        page: this.page
      }).then(response => {
        this.searchList = response.data.data
      })
    },
    searchKeyWord(text) {
      this.$refs.searchBar.setValue(text)
      this.onSearch(text)
    },
    clearHistorySearch() {
      this.historySearch = []
      setStorageSync(KEY_HISTORY_SEARCH, [])
    },
    changeHotSearch() {
      hotSearch().then(response => {
        this.hotSearch = response.data.data
      })
    },
    showBookDetail(text, index) {}
  },
  mounted() {
    this.openId = getStorageSync('openId')
    hotSearch().then(response => {
      this.hotSearch = response.data.data
    })
    this.page = 1
    this.hotSearchKeyword = this.$route.query.hotSearch
    this.historySearch = getStorageSync(KEY_HISTORY_SEARCH) || []
  },
  onPageScroll() {
    if(this.searchFocus) {
      this.searchFocus = false
    }
  },
  onReachBottom() {
    if(this.showList) {
      this.page++
      const searchWord = this.$refs.searchBar.getValue()
      search({
        keyword: searchWord,
        openId: this.openId,
        page: this.page
      }).then(response => {
        const { book } = response.data.data
        if(book && book.length > 0) {
          this.searchList.book.push(...book)
        } else {
          showToast('没有更多数据了')
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
