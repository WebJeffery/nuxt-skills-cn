---
name: "el-carousel"
description: "用于在有限空间内循环显示图像文本走马灯组件。当用户需要显示图像滑块、内容轮播旋转横幅时调用---

# Carousel 组件

Carousel 组件在有限空间内循环显示一系列图像文具有各种显示模式
## 何时使用

- 图像滑块
- 内容轮播
- 旋转横幅
- 产品展示

## 基本用法

```vue
<template>
  <el-carousel height="150px">
    <el-carousel-item v-for="item in 4" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## 运动模糊

```vue
<template>
  <el-carousel height="200px" motion-blur>
    <el-carousel-item v-for="item in 4" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## 指示
```vue
<template>
  <el-carousel indicator-position="outside">
    <el-carousel-item v-for="item in 4" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## 箭头

```vue
<template>
  <el-carousel :interval="4000" arrow="always">
    <el-carousel-item v-for="item in 4" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## 卡片模式

```vue
<template>
  <el-carousel type="card" height="200px">
    <el-carousel-item v-for="item in 6" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## 垂直

```vue
<template>
  <el-carousel height="200px" direction="vertical">
    <el-carousel-item v-for="item in 4" :key="item">
      <h3>{{ item }}</h3>
    </el-carousel-item>
  </el-carousel>
</template>
```

## API 参考
### Carousel 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| height | 走马灯高| `string` | `''` |
| initial-index | 初始激活幻灯片索| `number` | `0` |
| trigger | 指示器触发方式 | `'hover' \| 'click'` | `'hover'` |
| autoplay | 是否自动循环 | `boolean` | `true` |
| interval | 自动循环时间间毫秒) | `number` | `3000` |
| indicator-position | 指示器位置 | `'' \| 'none' \| 'outside'` | `''` |
| arrow | 箭头显示时| `'always' \| 'hover' \| 'never'` | `'hover'` |
| type | 走马灯类型 | `'' \| 'card'` | `''` |
| card-scale | 卡片模式下次要卡片缩放 | `number` | `0.83` |
| loop | 循环显示项目 | `boolean` | `true` |
| direction | 显示方向 | `'horizontal' \| 'vertical'` | `'horizontal'` |
| pause-on-hover | 鼠标悬停时暂停自动播| `boolean` | `true` |
| motion-blur | 启用运动模糊效果 | `boolean` | `false` |

### Carousel 事件

| Name | Description | Type |
|------|-------------|------|
| change | 激活幻灯片切换时触| `(current: number, prev: number) => void` |

### Carousel 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| Carousel-Item |

### Carousel 暴露

| Name | Description | Type |
|------|-------------|------|
| activeIndex | 激活幻灯片索| `number` |
| setActiveItem | 手动切换幻灯| `(index: string \| number) => void` |
| prev | 切换到上一张幻灯片 | `() => void` |
| next | 切换到下一张幻灯片 | `() => void` |

### CarouselItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| name | 项目名称 | `string` | `''` |
| label | 对应指示器文本 | `string \| number` | `''` |

## 最佳实践
1. 使用 `type="card"` 用于产品展示
2. 设置 `motion-blur` 以获得更平滑过3. 使用 `arrow="always"` 以获得更好可访问