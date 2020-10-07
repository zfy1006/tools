<template>
  <div class="page make-image-page" ref="makeImageRef">
    <input
      type="file"
      ref="uploadImageInput"
      id="upload"
      accept="image/*"
      style="display:none"
      @change="handleImageChange"
    />
    <div class="image-wrapper">
      <div class="image-area">
        <template v-if="resultSrc">
          <div class="result-area">
            <img :src="resultSrc" class="result-image" alt crossorigin="anonymous" />
          </div>
        </template>
        <template v-else>
          <template v-if="uploadImageSrc">
            <img :src="uploadImageSrc" class="upload-image" alt crossorigin="anonymous" />
            <img :src="templateList[templateIndex].url" class="template-img" v-if="templateIndex != null" alt />
            <span
              class="text-1 text"
              :style="templateList[templateIndex].text1.style"
              v-if="templateIndex != null && templateList[templateIndex].text1"
            >
              <span class="symbol">￥</span>
              {{ text1 }}
            </span>
            <span
              class="text-2 text"
              :style="templateList[templateIndex].text2.style"
              v-if="templateIndex != null && templateList[templateIndex].text2"
              >{{ text2 }}</span
            >
            <span
              class="text-3 text"
              :style="templateList[templateIndex].text3.style"
              v-if="templateIndex != null && templateList[templateIndex].text3"
              >{{ text3 }}</span
            >
            <span
              class="text-4 text price"
              :style="templateList[templateIndex].text4.style"
              v-if="templateIndex != null && templateList[templateIndex].text4"
              >{{ text4 }}</span
            >
          </template>
          <template v-else>
            <div class="empty" @click="uploadImageClick">
              <span>点击添加图片</span>
            </div>
          </template>
        </template>
      </div>
      <div class="btn-group">
        <div class="btn submit" @click="download" v-if="hasMaked">保存</div>
        <div class="btn submit" @click="handleImageMake(1)" v-if="uploadImageSrc && !hasMaked">合成图片</div>
        <div class="btn reset" @click="reset" v-if="uploadImageSrc" style="“margin-right:6px”">重置</div>
      </div>
    </div>
    <div class="controller-wrapper">
      <div class="template-list">
        <img
          :src="t.fullUrl"
          class="template-image"
          alt
          @click="handleInsertTemplate(index)"
          v-for="(t, index) in templateList"
          :key="index"
        />
      </div>
      <div class="form">
        <div class="form-item" v-if="templateList[templateIndex].text1">
          <div class="label">文字一</div>
          <input type="text" v-model="text1" class="text-input" />
          <input type="number" v-model="text1FontSize" class="font-size-input" />
        </div>
        <div class="form-item" v-if="templateList[templateIndex].text2">
          <div class="label">文字二</div>
          <input type="text" v-model="text2" class="text-input" />
          <input type="number" v-model="text2FontSize" class="font-size-input" />
        </div>
        <div class="form-item" v-if="templateList[templateIndex].text3">
          <div class="label">文字三</div>
          <input type="text" v-model="text3" class="text-input" />
          <input type="number" v-model="text3FontSize" class="font-size-input" />
        </div>
        <div class="form-item" v-if="templateList[templateIndex].text4">
          <div class="label">文字四</div>
          <input type="text" v-model="text4" class="text-input" />
          <input type="number" v-model="text4FontSize" class="font-size-input" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const downloadjs = require('downloadjs') //downloadjs  需要安装此依赖
import html2canvas from 'html2canvas'; //html2canvas  需要安装此依赖
let messageEle = null;
export default {
  layout: "toolsLayout",
  head() {
    return {
      title: "京客工具-制作营销图",
      meta: [],
    };
  },
  data() {
    return {
       text1: '',
      text1FontSize: '14',
      text2: '',
      text2FontSize: '14',
      text3: '',
      text3FontSize: '14',
      text4: '',
      text4FontSize: '14',
      uploadImageSrc: '',
      resultSrc: '',
      hasMaked: false,
      templateIndex: 0,
      templateList: [
        //1
        {
          fullUrl: '/images//template-1-full.png',
          url: '/images//template-1.png',
          text1: {
            default: '29.9',
            style: {
              bottom: '8px',
              left: '10px',
            },
          },
          text2: {
            default: '露露杏仁露 新鲜好滋味',
            style: {
              width: '300px',
              bottom: '8px',
              left: '267px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text3: {
            default: '仅限前1000名',
            style: {
              width: '300px',
              bottom: '42px',
              left: '282px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text4: {
            default: '劲爆内购价：',
            style: {
              bottom: '38px',
              left: '10px',
            },
          },
        },
        //2
        {
          fullUrl: '/images//template-2-full.png',
          url: '/images//template-2.png',
          text1: {
            default: '599',
            style: {
              bottom: '4px',
              left: '4px',
            },
          },
          text2: {
            default: '清爽酥脆不腻口清',
            style: {
              width: '300px',
              bottom: '16px',
              left: '266px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text3: {
            default: '实付到手价',
            style: {
              bottom: '35px',
              left: '4px',
            },
          },
        },
        //3
        {
          fullUrl: '/images//template-3-full.png',
          url: '/images//template-3.png',
          text1: {
            default: '799',
            style: {
              bottom: '30px',
              left: '4px',
            },
          },
          text2: {
            default: '全场满两件包邮',
            style: {
              width: '300px',
              bottom: '13px',
              left: '267px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text3: {
            default: '活动时间：8月31日12:00',
            style: {
              width: '300px',
              bottom: '58px',
              left: '294px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text4: {
            default: '活动价：',
            style: {
              bottom: '64px',
              left: '4px',
            },
          },
        },
        //4
        {
          fullUrl: '/images//template-4-full.png',
          url: '/images//template-4.png',
          text1: {
            default: '199',
            style: {
              bottom: '0px',
              left: '4px',
            },
          },
          text2: {
            default: '每满300减100',
            style: {
              width: '300px',
              bottom: '6px',
              left: '267px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text3: {
            default: '活动时间:9月18日24:00',
            style: {
              width: '300px',
              bottom: '37px',
              left: '264px',
              display: 'flex',
              'justify-content': 'center',
              transform: 'translate(-50%, 0)',
            },
          },
          text4: {
            default: '支付到手价',
            style: {
              bottom: '38px',
              left: '4px',
            },
          },
        },
      ]
    };
  },
  watch: {
    text1FontSize: function(newVal) {
      const element = document.getElementsByClassName('text-1')[0]
      const symbolElement = document.getElementsByClassName('symbol')[0]
      if(!this.uploadImageSrc || this.resultSrc) return;
      symbolElement.style.fontSize = isNaN(parseInt(newVal)) ? '12px' : parseInt(newVal) - 6 + 'px'
      element.style.fontSize = newVal + 'px'
    },
    text2FontSize: function(newVal) {
      const element = document.getElementsByClassName('text-2')[0]
      if(!this.uploadImageSrc || this.resultSrc) return;
      element.style.fontSize = newVal + 'px'
    },
    text3FontSize: function(newVal) {
      const element = document.getElementsByClassName('text-3')[0]
      if(!this.uploadImageSrc || this.resultSrc) return;
      element.style.fontSize = newVal + 'px'
    },
    text4FontSize: function(newVal) {
      const element = document.getElementsByClassName('text-4')[0]
      if(!this.uploadImageSrc || this.resultSrc) return;
      element.style.fontSize = newVal + 'px'
    },
  },
  async mounted() {
    messageEle = this.$refs.makeImageRef;
  },
  methods: {
    uploadImageClick() {
      this.$refs.uploadImageInput.click()
    },
    async handleImageChange(file) {
      const image_url = await this.handleFile(file)
      this.uploadImageSrc = image_url
      const temObj = this.templateList[this.templateIndex]
      if (!temObj) return
      this.text1 = temObj.text1.default
      this.text2 = temObj.text2.default
      if (temObj.text3) {
        this.text3 = temObj.text3.default
      }
      if (temObj.text4) {
        this.text4 = temObj.text4.default
      }
    },
    async handleFile(e) {
      const file = e.target.files[0]
      let formData = new FormData()
      formData.append('file', file)
      const res = await this.$request.UploadFile(formData)
      console.log('结果',res)
      if(res.code == 200) {
        return res.data
      }else {
        this.$message.error("添加图片失败", messageEle);
      }
    },
    reset() {
      this.uploadImageSrc = ''
      this.hasMaked = false
      if (this.resultSrc) this.resultSrc = ''
      this.handleInsertTemplate(1)
    },
    handleInsertTemplate(index) {
      this.templateIndex = index
      const temObj = this.templateList[index]
      if (!temObj) return
      this.text1 = temObj.text1.default
      this.text2 = temObj.text2.default
      if (temObj.text3) {
        this.text3 = temObj.text3.default
      }
      if (temObj.text4) {
        this.text4 = temObj.text4.default
      }
    },
    getScrollTop() {
      return window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0
    },
    getScrollLeft() {
      return window.pageXOffset || document.documentElement.scrollLeft || document.body.scrollLeft || 0
    },
    handleImageMake() {
      const targetElement = document.querySelector('.image-area')
      // const resultElement = document.querySelector('.result1')
      const Canvas = document.createElement('canvas')
      const width = targetElement.offsetWidth // 可见屏幕的宽
      const height = targetElement.offsetHeight // 可见屏幕的高
      const scale = window.devicePixelRatio // 设备的devicePixelRatio
      // 将Canvas画布放大scale倍，然后放在小的屏幕里，解决模糊问题
      Canvas.width = width * scale
      Canvas.height = height * scale
      Canvas.getContext('2d')
      const canvasParams = {
        canvas: Canvas,
        scale,
        useCORS: true,
        logging: true,
        scrollY: -this.getScrollTop(), //图片偏移问题
        scrollX: -10, //图片偏移问题
        width: width,
        hegiht: height,
      }
      html2canvas(targetElement, canvasParams).then((canvas) => {
        let dataURL = canvas.toDataURL('image/png') //将canvas对象转换为base64位编码
        this.resultSrc = dataURL
        this.hasMaked = true
      })
    },
    fake_click(obj) {
      var ev = document.createEvent('MouseEvents')
      ev.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null)
      obj.dispatchEvent(ev)
    },
    download() {
      downloadjs(this.resultSrc)
    }
  },
};
</script>
<style lang="scss" scoped>
@import "./index.scss";
</style>
