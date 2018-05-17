<template>
  <div class="recommend" ref="recommend">
    <Scroll class="recommend-content" ref="scroll">
      <div>
        <div v-if="recommends.length" class="slider-wrapper" >
          <div class="slider-content">
            <slider ref="slider">
              <div v-for = "(item, index) in recommends" :key="index">
                <a :href="item.linkUrl">
                  <img @load="loadImage" :src="item.picUrl" alt="index">
                </a>
              </div>
            </slider>
          </div>
        </div>
        <div class="recommend-list">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li v-for="(item, index) in discList" @click="selectItem(item)" :key="index" class="item">
              <div class="icon">
                <img width="60" height="60" v-lazy="item.imgurl">
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="loading-container" v-show="!discList.length">
        <loading></loading>
      </div>
    </Scroll>
    <router-view></router-view>
  </div>
</template>
<script type="text/ecmascript-6">
    import Slider from 'base/slider/slider'
    import { getRecommend, getDiscList } from 'api/recommend'
    import { ERR_OK } from 'api/config'
    import Scroll from 'base/scroll/scroll'
    import Loading from 'base/loading/loading'
    import {playlistMixin} from 'common/js/mixin'
    import {mapMutations} from 'vuex'

    export default {
      mixins: [playlistMixin],
      data() {
        return {
          recommends: [],
          discList: []
        }
      },
      created() {
        this._getRecommend()
        this._getDiscList()
      },
      actived() {
        setTimeout(() => {
          this.$refs.slider && this.$refs.slider.refresh()
        }, 20)
      },
      methods: {
        handlePlaylist(playlist) {
          const bottom = playlist.length > 0 ? '60px' : ''

          this.$refs.recommend.style.bottom = bottom
          this.$refs.scroll.refresh()
        },
        loadImage() {
          if (!this.checkloaded) {
            this.checkloaded = true
            setTimeout(() => {
              this.$refs.scroll.refresh()
            }, 20)
          }
        },
        selectItem(item) {
          this.$router.push({
            path: `/recommend/${item.dissid}`
          })
          this.setDist(item)
        },
        _getRecommend() {
          getRecommend().then((res) => {
            if (res.code === ERR_OK) {
              this.recommends = res.data.slider
            }
          })
        },
        _getDiscList() {
          getDiscList().then((res) => {
            if (res.code === ERR_OK) {
              this.discList = res.data.list
            }
          })
        },
        ...mapMutations({
          setDist: 'SET_DISC'
        })
      },
      components: {
        Slider,
        Scroll,
        Loading
      }
    }
</script>
<style lang="stylus" scoped rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .recommend
    position: fixed
    width: 100%
    top: 88px
    bottom: 0
    .recommend-content
      height: 100%
      overflow: hidden
      .slider-wrapper
        position: relative
        width 100%
        height: 0
        padding-top: 40%
        overflow: hidden
        .slider-content
          position: absolute
          top: 0
          left: 0
          width 100%
          height 100%
      .recommend-list
        .list-title
          height: 65px
          line-height: 65px
          text-align: center
          font-size: $font-size-medium
          color: $color-theme
        .item
          display:flex
          box-sizing: border-box
          align-items: center
          padding: 0 20px 20px 20px
          .icon
            flex: 0 0 60px
            width: 60px
            padding-right: 20px
          .text
            display: flex
            flex-direction: column
            justify-content: center
            flex: 1
            line-height: 20px
            overflow: hidden
            font-size: $font-size-medium
            .name
              margin-bottom: 10px
              color: $color-text
            .desc
              color: $color-text-d
</style>


