<template>
  <span title="点击在最终幻想XIV中文维基上查看详情">
    <a :href="wikiLink" target="_blank">
      <div class="action-container">
        <div class="cover"></div>
        <img :src="icon" alt="物品" class="no-zoom" />
      </div>
      <span>{{ name }}</span>
    </a>
  </span>
</template>
<style lang="stylus" scoped>
img
  vertical-align middle
  width 1em
  height 1em
  margin-bottom .2em
  cursor pointer!important
.action-container
  display inline-block
  position relative
.cover
  width 100%
  height 100%
  position absolute
  top 0
  left 0
  border-radius 15%
  pointer-events none
  background radial-gradient(circle at 50% -430%, rgba(255,255,255,0.6) 70%, rgba(255,255,255,0) 65%)
  box-shadow inset 0px 4px 4px 2px rgba(255,255,255,0.3), inset 0px -2px 4px 2px rgba(255,255,255,0.1)
</style>
<script>
import 'isomorphic-fetch'

const cache = {}

export default {
  props: {
    name: String,
    type: {
      type: String,
      default: 'side'
    }
  },
  data: function() {
    return {
      icon: '/images/icons/060051.png'
    }
  },
  serverPrefetch() {
    return this.setItemIcon()
  },
  mounted() {
    this.setItemIcon()
  },
  methods: {
    async searchItem(name) {
      const json = await (await fetch(
        'https://cafemaker.wakingsands.com/search?indexes=Item&limit=1&string=' +
          encodeURIComponent(name)
      )).json()
      return json.Results && json.Results[0]
    },
    async getItemIcon(name) {
      const item = await this.searchItem(name)
      if (!item || !item.Icon) {
        return '/images/icons/060051.png'
      }
      return 'https://cafemaker.wakingsands.com' + item.Icon
    },
    async setItemIcon() {
      if (cache[this.name]) {
        this.icon = cache[this.name]
        return
      }
      this.icon = cache[this.name] = await this.getItemIcon(this.name)
    }
  },
  computed: {
    wikiLink() {
      return (
        'https://ff14.huijiwiki.com/wiki/' +
        encodeURIComponent('物品') +
        ':' +
        encodeURIComponent(this.name)
      )
    }
  }
}
</script>
