<script setup lang="ts">
import { useDrawPoster } from 'u-draw-poster'
import drawQrCode from 'u-draw-poster/plugins/drawQrCode'

const imgUrl = ref('')
const options = ref({
  plugins: [drawQrCode()],
  loading: true,
  debug: true, // 发布为正式版时关闭该选项
  width: 650,
  height: 920
})
onReady(async () => {
  // 创建绘制工具
  const dp = await useDrawPoster('canvas', options.value)
  const w = options.value.width
  const h = options.value.height
  // 绘制基本背景
  dp.draw((ctx) => {
    ctx.fillStyle = '#ffffff'
    ctx.fillRoundRect(0, 0, w, h, 12)
    ctx.clip()
    ctx.fillStyle = '#E3712A'
    ctx.fillRect(0, 0, w, h - 160)
    ctx.fillStyle = '#ffffff'
    ctx.textAlign = 'center'
    ctx.font = 'bold 32px PingFang SC'
    ctx.fillText(' 分享海报 ', w / 2, 50)
  })
  // 绘制图片内容
  dp.draw(async (ctx) => {
    await Promise.all([
      ctx.drawImage('/static/poster/logo1.png', 20, 20, 35, 35),
      ctx.drawImage('/static/poster/tp.png', 19, 86, 612, 459),
      ctx.drawImage('/static/poster/bw.png', 188, 559, 274, 50)
    ])
    // 用户二维码
    await ctx.drawQrCode({
      x: 518,
      y: 776,
      text: 'http://www.baidu.com',
      size: 90
    })
    // 用户头像
    await ctx.drawRoundImage('/static/logo.png', 39, 790, 90, 90, 100)
  })
  // 绘制中间文字内容
  dp.draw((ctx) => {
    ctx.fillStyle = '#ffffff'
    ctx.font = 'bold 32px PingFang SC'
    ctx.fillText('To倪好:', 34, 660)
    ctx.font = '28px PingFang SC'
    ctx.fillWarpText({
      content: '当你觉得晚了的时候，恰恰是开始的时候，未来可期、前程似锦、一路向前吧。dddddddddddddd',
      maxWidth: 527,
      x: 81,
      y: 700,
      layer: 2
    })
  })
  // 绘制底部文字内容
  dp.draw((ctx) => {
    ctx.fillStyle = '#333333'
    ctx.font = '28px PingFang SC'
    ctx.fillText('叽叽喳喳', 145, 820)
    ctx.font = '24px PingFang SC'
    ctx.fillText('邀请您一起聆听声音', 145, 866)
    ctx.font = '21px PingFang SC'
    ctx.fillText('扫码聆听', 521, 896)
  })
  imgUrl.value = await dp.create()
})

const handelSaveImg = () => {
  uni.saveImageToPhotosAlbum({
    filePath: imgUrl.value,
    success: () => {
      showToast({
        title: 'success'
      })
    },
    fail: () => {
      showToast({
        title: 'error'
      })
    }
  })
}
</script>

<template>
  <div class="container">
    <image class="img" show-menu-by-longpress :src="imgUrl" />
    <div @click="handelSaveImg" class="tip">
      <u-icon name="fingerprint"></u-icon>
      点击此处或长按图片保存到本地
    </div>
    <div style="position: fixed; top: 999999999999999999999rpx">
      <canvas id="canvas" type="2d" style="width: 650px; height: 920px" />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  width: 100%;
  height: 100%;
}
.img {
  display: block;
  width: 650rpx;
  height: 920rpx;
  margin: 30rpx auto 0;
}
.tip {
  display: flex;
  justify-content: center;
  margin-top: 30rpx;
}
</style>
