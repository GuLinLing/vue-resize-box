# vue-resize-box

## 使用方法1
### 导入组件： import ResizeBox from 'vue-resize-box/src/main.vue'
### 注册组件：components: {[ResizeBox.name]: ResizeBox}
### 使用组件：\<ResizeBox/\>xxx\</ResizeBox\>

## 使用方法2
### npm install vue-resize-box
### 导入组件：import ResizeBox from 'vue-resize-box'
### 注册组件：Vue.use(ResizeBox)
### 使用组件：\<ResizeBox/\>xxx\</ResizeBox\>

## Attributes
### | 参数 | 说明 | 类型 | 默认值 |
### | min | 最小宽高 | object | {width: 0, height: 0} |
### | max | 最大宽高 | object | {width: 0, height: 0} |
### | move | 移动手柄配置 | object | {t: true, l: true, r: true, b: true, tl: true, tr: true, bl: true, br: true} |
### | disabled | 是否禁用 | object | false |
