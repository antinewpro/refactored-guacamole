<template>
  <div class="header">
    <div class="content-wrapper">

      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>

      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="supports">
          <v-icon :type='seller.supports[0].type'></v-icon>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>

      <div v-if="seller.supports" class="supports-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>

    </div>

    <div class="bulletin-wrapper">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>

    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>

    <v-details :seller='seller' v-if="detailShow"></v-details>
  </div>
</template>

<script>
import details from './detail/details'
import icon from 'components/icon/icon'

export default {
  props: {
    seller: Object
  },
  data() {
    return {
      detailShow: false
    }
  },
  methods: {
    showDetail() {
      this.detailShow = !this.detailShow
    }
  },
  components: {
    'v-details': details,
    'v-icon': icon
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl';

  .header
    position relative
    color #ffffff
    overflow hidden
    // background #999999
    background-color rgba(7, 17, 27, 0.5)
    .content-wrapper
      position relative
      padding 24px 12px 18px 24px
      font-size 0 // 可消除子元素间距
      .avatar
        display inline-block
        vertical-align top
        img
          border-radius 2px
      .content
        display inline-block
        margin-left 16px
        .title
          margin 2px 0 8px 0
          white-space nowrap
          .brand
            display inline-block
            vertical-align top
            width 30px
            height 18px
            bg-img('brand')
            background-size 30px 18px
            background-repeat no-repeat
          .name
            margin-left 6px
            font-size 16px
            line-height 18px
            font-weight bold
        .description
          margin-bottom 10px
          line-height 12px
          font-size 12px
        .supports
          .text
            line-height 12px
            font-size 10px // iphone标准 在chrome下是看不出的,chrome字体最小是12px

      .supports-count
        position absolute // 相对于content-wrapper
        right 12px
        bottom 14px
        padding 0px 8px
        line-height 24px
        height 24px
        border-radius 14px
        background rgba(0, 0, 0, .2)
        text-align center
        .count
          vertical-align top
          font-size 10px
        .icon-keyboard_arrow_right
          margin-left 2px
          line-height 24px
          font-size 10px
    .bulletin-wrapper
      position relative
      height 28px
      line-height 28px
      padding 0 22px 0 12px
      white-space nowrap
      overflow hidden
      text-overflow ellipsis
      background rgba(7, 17, 27, .2)
      .bulletin-title
        display inline-block
        vertical-align top
        margin-top 8px
        width 22px
        height 12px
        bg-img('bulletin')
        background-size contain
        background-repeat no-repeat
      .bulletin-text
        vertical-align top
        font-size 10px
        margin 0 4px
      .icon-keyboard_arrow_right
        position absolute
        font-size 10px
        right 12px
        top 8px
    .background
      position absolute
      top 0
      left 0
      width 100%
      height 100%
      z-index -1
      filter blur(10px) // 滤镜
</style>
