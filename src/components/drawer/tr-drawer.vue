<template>
  <div class="tr-drawer" :class="wrapperClass" :style="{ width: drawerWidth }">
    <slot></slot>
    <div class="tr-drawer__operation">
      <div v-if="showClose" class="tr-drawer__operation-close" @click="close">
        <img src="../../assets/close.svg" alt="">
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    name: 'TrDrawer',
    props: {
      collapse: {
        type: Boolean,
        default: false
      },
      position: {
        type: String,
        default: 'right'
      },
      width: [String, Number],
      autoHide: {
        type: Boolean,
        default: true
      },
      showClose: {
        type: Boolean,
        default: true
      },
      customClass: {
        type: String,
        default: ''
      },
      beforeClose: {
        type: Function,
        default: function(callback) {
          callback()
        }
      }
    },
    model: {
      prop: 'collapse',
      event: 'change'
    },
    data() {
      return {
        collapsed: this.collapse
      }
    },
    watch: {
      collapse: {
        handler(val, oldVal) {
          // console.log('collapsed', val, oldVal);
          let that = this;
          if(!val && oldVal) {
            // open
            // => add mask
            let mask = document.createElement('div');
            mask.className = 'tr-drawer-mask';

            // if autoHide
            if(this.autoHide) {
              mask.onclick = function() {
                that.close()
              };
            }

            document.body.appendChild(mask);

          } else if(val && !oldVal) {
            // close
            // => remove mask
            let masks = document.getElementsByClassName('tr-drawer-mask');
            for(let mask of masks) {
              document.body.removeChild(mask)
            }
          }
        },
        deep: true
      }

    },
    computed: {
      wrapperClass() {
        let wrapperClass = '';
        wrapperClass += `tr-drawer--${this.position}`;

        let collapsedClass = '';
        if(this.collapse) {
          collapsedClass = ' tr-drawer--collapsed'
        } else {
          this.$emit('open')
        }

        wrapperClass += collapsedClass;

        //custom class
        wrapperClass += ' ' + this.customClass;
        return wrapperClass
      },
      drawerWidth() {
        let result = '';
        if((typeof this.width) === 'number') {
          result = this.width + 'px'
        } else if((typeof this.width) === 'string') {
          result = this.width
        } else {
          result = '600px'
        }
        return result
      }
    },
    methods: {
      _reactOnMask() {
          let val = this.collapsed;
          let that = this;
          if(!val) {
              // open
              // => add mask
              let mask = document.createElement('div');
              mask.className = 'tr-drawer-mask';

              // if autoHide
              if(this.autoHide) {
                mask.onclick = function() {
                  that.close()
                };
              }

              document.body.appendChild(mask);

          } else if(val) {
              // close
              // => remove mask
              let masks = document.getElementsByClassName('tr-drawer-mask');
              for(let mask of masks) {
                  document.body.removeChild(mask)
              }
          }
      },
      close() {
        this.beforeClose(this._close)
      },
      _close() {
        this.$emit('close');
        this.$emit('change', true)
      }
    },
    created() {
        this._reactOnMask()
    },
    mounted() {
    }
  }
</script>
<style lang="scss" scoped>
  .tr-drawer {
    position: fixed;
    z-index: 100;
    padding: 20px;
    box-sizing: border-box;

    background-color: #fff;
    box-shadow: -5px 0 40px .5px rgba(0,0,0, 0.2);
    opacity: 1;
    transition: .3s ease-out;

    &--collapsed {
      opacity: 0;
      width: 0 !important;
      transition: .1s ease-out;
    }

    &--right {
      right: 0;
      top: 0;
      bottom: 0;

      .tr-drawer__operation {
        position: absolute;
        left: -70px;
        top: 25px;
        img {
          box-sizing: content-box;
          width: 20px;
          transition: .2s ease-out;
          color: #979797;

          background-color: #fff;
          padding: 12px;
          border-radius: 50%;
          &:hover {
            cursor: pointer;
            transition: .2s ease-out;
            background-color: #f1f1f1;
            transform: rotate(90deg);
          }
        }
      }
    }
    &--top {

    }
    &--left {

    }
    &--bottom {

    }
  }
</style>

<style lang="scss">
  .tr-drawer-mask {
    position: fixed;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    background-color: #000;
    opacity: 0.7;
    z-index: 99;
  }
</style>
