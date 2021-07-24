<template>
  <main class="page">
    <slot name="top" />

    <Content class="theme-antdocs-content" />
    <div class="right-container" v-if="!!anchorList">
      <a-anchor :target-offset="targetOffset">
        <a-anchor-link v-for="(item, index) in anchorList" :key="index" :href="'#'+item.slug" :title="item.title" />
      </a-anchor>
    </div>

    <PageEdit />

    <PageNav v-bind="{ sidebarItems }" />

    <slot name="bottom" />
  </main>
</template>

<script>
import PageEdit from '@theme/components/PageEdit.vue'
import PageNav from '@theme/components/PageNav.vue'

export default {
  components: { PageEdit, PageNav },
  props: ['sidebarItems'],
  data() {
    return {
      flag: false,
      allList:[],
      targetOffset: undefined,
      anchorList: []
    }
  },
  watch: {
    sidebarItems: {
      immediate: true,
      handler(val) {
        this.allList = []
        let _anchorList = []
        this.get_children(val)
        for(let i = 0; i< this.allList.length; i++) {
          let _routePath =this.$route.path.split('/')[this.$route.path.split('/').length-2] +'/'+ this.$route.path.split('/')[this.$route.path.split('/').length-1]
          let _configPath =this.allList[i].path.split('/')[this.allList[i].path.split('/').length-2] +'/'+ this.allList[i].path.split('/')[this.allList[i].path.split('/').length-1]
          if (_routePath == _configPath) {
            _anchorList = this.allList[i].headers
            break
          }
        }
        this.anchorList = _anchorList
      }
    }
  },
  mounted() {
    this.targetOffset = window.innerHeight / 2;
  },
  methods: {
    get_children(list) {
      list.forEach(item => {
        if (!!item.children) {
          this.get_children(item.children)
        } else {
          this.allList.push(item)
        }
      })
      
    }
  }
}
</script>

<style lang="less">
@import '../styles/palette.less';
@import "../styles/wrapper.less";
.page {
  padding-bottom: 2rem;
  display: block;
}
@media (max-width: @MQMobile) {
  .page {
    margin-top: -@navbarHeight;
  }
  .right-container {
    display: none;
  }
}
.right-container {
  position: fixed;
  right: 10px;
  top: 100px;
  z-index: 10;
  width: 130px;
}
</style>
