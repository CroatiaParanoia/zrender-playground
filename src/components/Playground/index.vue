<script lang="ts" setup>
import * as zrender from 'zrender'
import hotkey from 'hotkeys-js'
import { getStartPoint } from '../Playground/utils'
import { CELL_HEIGHT, CELL_WIDTH, COL_CARD_COUNT, LINE_WIDTH, ROW_CARD_COUNT } from '../Playground/constant'
import Roamable from './Roamable'
const playgroundRef = ref(null)

let zr: ReturnType<typeof zrender.init>

const rootGroup = new zrender.Group({
})
const drawRect = () => {
  if (!zr)
    return
    // 绘制网格
  const grid = new zrender.Group()
  const width = zr.getWidth() // 网格宽度
  const height = zr.getHeight() // 网格高度
  const lineColor = '#ccc' // 线条颜色

  const { maxHeight, maxWidth } = getStartPoint({ width, height }, LINE_WIDTH)

  for (let x = 0; x < ROW_CARD_COUNT; x++) {
    const localX = x * CELL_WIDTH + x * LINE_WIDTH + LINE_WIDTH / 2

    const line = new zrender.Line({
      shape: {
        x1: localX,
        y1: 0,
        x2: localX,
        y2: maxHeight,
      },
      style: {
        lineWidth: LINE_WIDTH,
        stroke: lineColor,
      },
    })
    line.cursor = 'default'
    grid.add(line)
  }

  for (let x = 0; x < COL_CARD_COUNT; x++) {
    const localY = x * CELL_HEIGHT + x * LINE_WIDTH + LINE_WIDTH / 2
    const line = new zrender.Line({
      shape: {
        x1: 0,
        y1: localY,
        x2: maxWidth,
        y2: localY,

      },
      style: {
        lineWidth: LINE_WIDTH,
        stroke: lineColor,
      },
    })
    line.cursor = 'default'
    grid.add(line)
  }

  for (let i = 0; i < ROW_CARD_COUNT; i++) {
    for (let j = 0; j < COL_CARD_COUNT; j++) {
      const x = i * CELL_WIDTH + i * LINE_WIDTH + LINE_WIDTH / 2
      const y = j * CELL_HEIGHT + j * LINE_WIDTH + LINE_WIDTH / 2
      const localCircleGroup = new zrender.Group()
      const circle = new zrender.Circle({
        shape: {
          cx: x,
          cy: y,
          r: 50,

        },
        style: {
          fill: 'rgba(255, 0, 0, 0.5)',
        },
        z: 2,
        // textContent: ,
      })
      const text = new zrender.Text({
        style: {
          text: '*',
          fill: '#000',
          fontSize: 44,
          align: 'center',
          verticalAlign: 'middle',
        },
        x: circle.shape.cx,
        y: circle.shape.cy,
      })

      circle.name = 'Chess'

      circle.states = {

      }

      localCircleGroup.add(circle)
      localCircleGroup.add(text)

      //   circle.setTextContent(text)
      grid.add(localCircleGroup)
    //   grid.onmouseup = (e) => {
      // console.log(e)
    //   }
    }
  }

  //   grid.setPosition([startX, startY])

  rootGroup.add(grid)
}

onMounted(() => {
  zr = zrender.init(playgroundRef.value)
  const rulerLength = 1000
  rootGroup.add(new zrender.Line({
    shape: {
      x1: 0,
      y1: -rulerLength,

      x2: 0,
      y2: rulerLength,
    },
    style: {
      lineWidth: 3,
      stroke: 'red',
      lineDash: 'dashed',
    },
  }))

  rootGroup.add(new zrender.Line({
    shape: {
      x1: -rulerLength,
      y1: 0,
      x2: rulerLength,
      y2: 0,
    },
    style: {
      lineWidth: 3,
      stroke: 'red',
      lineDash: 'dashed',
    },
  }))

  const roamable = new Roamable(zr, rootGroup)

  roamable.init()

  drawRect()

  zr.add(rootGroup)
})

hotkey.setScope('global')
</script>

<template>
  <div>
    <div
      ref="playgroundRef"
      class="playground container w-1000px h-1000px border border-light-400 border-width-2 rounded-4 mx-auto"
    >
      Playground123
    </div>
  </div>
</template>

<style></style>
