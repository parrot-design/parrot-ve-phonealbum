# @parrotjs/vue-photoalbum 2.x

> 这是一个可以预览照片的组件 支持ssr模式 大致功能有放大缩小图片、图片轮播、图片宫格展示等功能。

```js
<template>
  <div>
    <img
      :src="item.src"
      v-for="(item, index) in imageList"
      :key="index" 
      @click="handleClick(index)"
    />
    <phonealbum ref="phonealbum" :imgsArr="imageList" />
  </div>
</template>
               
<script> 

import phonealbum from '@parrotjs/vue-photoalbum';
import "@parrotjs/vue-photoalbum/dist/vue-photoalbum.css";

export default {
  data() {
    return { 
      imageList: [
        { src: "https://www.chhes.com/pic/IMG_5988.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5987.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5986.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5985.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5984.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5983.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5982.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5981.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5980.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5979.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5978.JPG" },
        { src: "https://www.chhes.com/pic/IMG_5977.JPG" },
      ] 
    };
  },
  components: { 
    phonealbum
  }, 
  methods: {
    handleClick(index) {
      //传递index
      this.$refs.phonealbum.show({
        index
      }) 
    },
  }, 
};
</script> 

```
![1](https://user-images.githubusercontent.com/77631518/141070647-7d09c926-ca0c-4cad-bfee-877c4c2759fd.jpg)

 
