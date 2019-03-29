<template>
  <div ref="display"></div>
</template>
<script>
  import randomStr from '../../utils/random_str.js'

  export default {
    props: {
      code: {
        type: String,
        default: ''
      }
    },
    // 父级传递 code 后，将其分割，并保存在 data 的 html、js、css 中，后续使用
    data() {
      return {
        html: '',
        js: '',
        css: '',
        id: randomStr()
      }
    }
    methods: {
      getSource (source, type) {
        // 使用正则，基于 <> 和 </> 的特性进行分割
        const regex = new RegExp(`<${type}[^>]*>`)
        let openingTag = source.match(regex)

        if (!openingTag) return ''
        else openingTag = openingTag[0]

        return source.slice(source.indexOf(openingTag) + openingTag.length, source.lastIndexOf(`</${type}>`))
      },
      splitCode () {
        const script = this.getSource(this.code, 'script').replace(/export default/, 'return ')
        const style = this.getSource(this.code, 'style')
        const template = '<div id="app">' + this.getSource(this.code, 'template') + '</div>'

        this.js = script
        this.css = style
        this.html = template
      },
      renderCode () {
        this.splitCode()

        if (this.html !== '' && this.js !== '') {
          const parseStrToFunc = new Function(this.js)()

          parseStrToFunc.template =  this.html
          const Component = Vue.extend( parseStrToFunc )
          this.component = new Component().$mount()

          this.$refs.display.appendChild(this.component.$el)
        }
        if (this.css !== '') {
          const style = document.createElement('style')
          style.type = 'text/css'
          style.id = this.id
          style.innerHTML = this.css
          document.getElementsByTagName('head')[0].appendChild(style)
        }
      },
      destroyCode () {
        const $target = document.getElementById(this.id)
        if ($target) $target.parentNode.removeChild($target)

        if (this.component) {
          this.$refs.display.removeChild(this.component.$el)
          this.component.$destroy()
          this.component = null
        }
      }
    },
    mounted () {
      this.renderCode()
    },
    beforeDestroy () {
      this.destroyCode()
    },
    watch: {
      code () {
        this.destroyCode()
        this.renderCode()
      }
    }
  }
</script>