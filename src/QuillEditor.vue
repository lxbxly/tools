<template>
  <div class="quill-editor-container" :class="{ 'fullscreen': isFullscreen }" ref="container">
    <div ref="editor" class="quill-editor"></div>
  </div>
</template>

<script>
import Quill from "quill";
import 'quill/dist/quill.snow.css';

// 中文配置
const QuillLang = {
  'formats': {
    'bold': '粗体',
    'italic': '斜体',
    'underline': '下划线',
    'strike': '删除线',
    'blockquote': '引用',
    'code-block': '代码块',
    'header': '标题',
    'list': '列表',
    'script': '脚本',
    'indent': '缩进',
    'direction': '方向',
    'size': '大小',
    'color': '颜色',
    'background': '背景',
    'font': '字体',
    'align': '对齐',
    'clean': '清除格式',
    'fullscreen': '全屏'
  },
  'modules': {
    'toolbar': '工具栏',
    'history': '历史记录',
    'image-tooltip': '图片提示',
    'link-tooltip': '链接提示'
  },
  'shortcuts': {
    'redo': '重做',
    'undo': '撤销'
  }
};

// 注册中文语言
Quill.register('modules/lang', QuillLang, true);

export default {
  name: 'QuillEditor',
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      quill: null,
      isFullscreen: false
    }
  },
  mounted() {
    this.initEditor()
  },
  watch: {
    value: {
      handler(newVal) {
        if (this.quill && newVal !== this.quill.root.innerHTML) {
          this.quill.root.innerHTML = newVal
        }
      },
      immediate: true
    }
  },
  methods: {
    initEditor() {
      this.quill = new Quill(this.$refs.editor, {
        theme: 'snow',
        modules: {
          toolbar: {
            container: [
              ['bold', 'italic', 'underline', 'strike'],
              ['blockquote', 'code-block'],
              [{ 'header': 1 }, { 'header': 2 }],
              [{ 'list': 'ordered' }, { 'list': 'bullet' }],
              [{ 'script': 'sub' }, { 'script': 'super' }],
              [{ 'indent': '-1' }, { 'indent': '+1' }],
              [{ 'direction': 'rtl' }],
              [{ 'size': ['small', false, 'large', 'huge'] }],
              [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
              [{ 'color': [] }, { 'background': [] }],
              [{ 'font': [] }],
              [{ 'align': [] }],
              ['image'],
              ['fullscreen'],
              ['clean']
            ],
            handlers: {
              'image': this.imageHandler,
              'fullscreen': this.toggleFullscreen
            }
          }
        },
        placeholder: '开始编辑...'
      })

      this.quill.on('text-change', () => {
        this.$emit('input', this.quill.root.innerHTML)
      })
    },
    imageHandler() {
      const input = document.createElement('input');
      input.setAttribute('type', 'file');
      input.setAttribute('accept', 'image/*');
      input.click();

      input.onchange = () => {
        const file = input.files[0];
        if (file) {
          // 这里可以实现图片上传逻辑
          // 示例：模拟上传成功，返回一个图片URL
          const reader = new FileReader();
          reader.onload = (e) => {
            const imgUrl = e.target.result;
            const range = this.quill.getSelection();
            this.quill.insertEmbed(range.index, 'image', imgUrl);
          };
          reader.readAsDataURL(file);
        }
      };
    },
    toggleFullscreen() {
      this.isFullscreen = !this.isFullscreen;
      if (this.isFullscreen) {
        document.body.style.overflow = 'hidden';
        this.$refs.container.style.position = 'fixed';
        this.$refs.container.style.top = '0';
        this.$refs.container.style.left = '0';
        this.$refs.container.style.width = '100%';
        this.$refs.container.style.height = '100%';
        this.$refs.container.style.zIndex = '9999';
        this.$refs.container.style.margin = '0';
      } else {
        document.body.style.overflow = '';
        this.$refs.container.style.position = '';
        this.$refs.container.style.top = '';
        this.$refs.container.style.left = '';
        this.$refs.container.style.width = '80%';
        this.$refs.container.style.height = '';
        this.$refs.container.style.zIndex = '';
        this.$refs.container.style.margin = '0 auto';
      }
    }
  }
}
</script>

<style scoped>
.quill-editor-container {
  width: 80%;
  margin: 0 auto;
  border: 1px solid #ddd;
  border-radius: 4px;
  overflow: hidden;
  transition: all 0.3s ease;
  resize: vertical;
  min-height: 300px;
  max-height: 800px;
}

.quill-editor-container.fullscreen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 9999;
  margin: 0;
  resize: none;
}

.quill-editor {
  height: 100%;
  min-height: 300px;
}
</style>

