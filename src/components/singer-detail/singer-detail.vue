<template>
  <transition name="slider">
    <music-list :songs="songs" :title="title" :bgImage="bgImages"></music-list>
  </transition>
</template>

<script>
import { mapGetters } from 'vuex'
import { getSingerDetail } from '../../api/singer'
import { createSong } from '../../common/js/song'

import { ERR_OK } from '../../api/config'
import musicList from 'components/music-list/music-list'

export default {
  name: 'singer-detail',
  components: {
    musicList
  },
  data () {
    return {
      songs: []
    }
  },
  created () {
    this._getDetail()
  },
  computed: {
    bgImages () {
      return this.singer.avatar
    },
    title () {
      return this.singer.name
    },
    ...mapGetters([
      'singer'
    ])
  },
  methods: {
    _getDetail () {
      if (!this.singer.id) {
        this.$.router.push('/singer')
        return
      }
      getSingerDetail(this.singer.id).then(res => {
        if (res.code === ERR_OK) {
          this.songs = this._normalizeSongs(res.data.list)
        }
      })
    },
    _normalizeSongs (list) {
      let ret = []
      list.forEach(item => {
        let {musicData} = item
        if (musicData.songid && musicData.albummid) {
          ret.push(createSong(musicData))
        }
      })
      return ret
    }
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
@import '~common/stylus/variable'
  .singer-detail
    position fixed
    z-index 100
    top 0
    left 0
    right 0
    bottom 0
    background $color-background
  .slider-enter-active,.slider-leave-active
    transition all 0.3s
  .slider-enter,.slider-leave-to
    transform translate3d(100%, 0 ,0)
</style>
