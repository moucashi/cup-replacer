<template>
  <canvas id="canvas" width="1000" height="1000" ref="canvasRef"></canvas>
  <div class="mx-2">
    <template v-for="{name, urls} in fields">
      <div class="mb-2" v-if="name !== 'human'">
        <div class="bg-white rounded ">
          <div class="text-black px-2">{{ name }}</div>
          <div class="flex">
            <img class="m-2 rounded cursor-pointer" style="width: 80px; height: 80px; object-fit: cover" v-for="url in urls" :src="url" @click="onImageClick(name, url)"/>
          </div>
        </div>
      </div>
    </template>
  </div>
</template>
<script setup>
import { onMounted, ref } from "vue";
import { fabric } from "fabric"
import cup1 from "@/assets/cup2.1.webp"
import cup2 from "@/assets/cup2.2.webp"
import cup3 from "@/assets/cup2.3.webp"
import cup4 from "@/assets/cup2.4.webp"
import cup5 from "@/assets/cup2.5.webp"

import bg1 from "@/assets/bg1.png"
import bg2 from "@/assets/bg2.png"
import bg3 from "@/assets/bg3.png"

import human from "@/assets/human.png"

import arm1 from "@/assets/arm1.png"
import arm2 from "@/assets/arm2.png"

import hair1 from "@/assets/hair1.png"
import hair2 from "@/assets/hair2.png"
import hair3 from "@/assets/hair3.png"

import dog1 from "@/assets/dog1.png"
import dog2 from "@/assets/dog2.png"
import dog3 from "@/assets/dog3.png"

import drink1 from "@/assets/drink1.png"
import drink2 from "@/assets/drink2.png"


let canvasRef = ref();

let fields = ref([])

let callbackByNames = {}

let onImageClick = (name, url) => {
  let callbackByUrls = callbackByNames[name]
  if (callbackByUrls) {
    let callback = callbackByUrls[url]
    callback()
  }
}

onMounted(async () => {

  console.log("cup1", cup1)

  let canvas = new fabric.Canvas(canvasRef.value)

  canvas.backgroundColor = "#ffffff"

  let map = new Map()

  let urls = [
    cup1,
    cup2,
    cup3,
    cup4,
    cup5,
    bg1,
    bg2,
    bg3,
    human,
    arm1,
    arm2,
    hair1,
    hair2,
    hair3,
    dog1,
    drink1,
    drink2
  ]

  await Promise.all(urls.map(url => new Promise(r => {
    fabric.Image.fromURL(url, img => {
      map.set(url, img)
      r(img)
    })
  })))

  let cup1Img = map.get(cup1)
  let drinkImg = null

  addImage("cup", [cup1, cup2, cup3, cup4, cup5], {
    left: (1000 - cup1Img.width) / 2,
    top: (1000 - cup1Img.height) / 2,
  })

  addImage("bg1", [bg1, bg2, bg3], {
    top: 245,
    left: 370,
    scaleX: 1.35,
    scaleY: 1.35
  })

  addImage("human", [human], {
    top: 410,
    left: 570,
  })

  addImage("drink", [drink1, drink2], {
    top: 465,
    left: 707,
    scaleX: 0.35,
    scaleY: 0.35
  })

  addImage("arm", [arm1, arm2], {
    top: 471,
    left: 691,
  })

  addImage("hair", [hair1, hair2, hair3], {
    top: 400,
    left: 580,
    scaleX: 0.35,
    scaleY: 0.35
  })

  addImage("dog", [dog1, dog2, dog3], {
    top: 440,
    left: 355,
    scaleX: 0.9,
    scaleY: 0.9
  })

  function addImage(name, urls, options) {
    let img = map.get(urls[0])
    console.log(`get ${name} img`, () => img)
    canvas.add(img.set(options))

    if (name === "drink") {
      drinkImg = img
    }

    fields.value.push({
      name,
      urls
    })

    let oldImg = img
    callbackByNames[name] = Object.fromEntries(urls.map(url => [url, () => {
      oldImg.setSrc(url, () => canvas.renderAll())
      if (name === "arm") {
        drinkImg.visible = url !== urls[1]
      }
    }]))
  }
})


</script>
<style scoped>

</style>
