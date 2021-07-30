<template>
  <div
    v-show="!!$scopedSlots.activator"
    ref="activator"
    @click="toggle()"
    @keydown.stop="onActivatorKeyDown"
    @focusin="onActivatorFocus"
    @mouseover="mount"
  >
    <slot name="activator" :active="shown" :show="() => show()" />

    <transition name="fade">
      <div v-show="shown" ref="body" class="popover-body" @click="onBodyClick">
        <slot v-if="shown" :active="shown" :hide="hide" />
      </div>
    </transition>
  </div>
</template>

<script>
import { createPopper } from '@popperjs/core'
export default {
  inheritAttrs: false,
  model: {
    prop: 'active',
    event: 'active-change'
  },
  props: {
    active: Boolean,

    // https://popper.js.org/docs/v2/constructors/#placement
    placement: { type: String, default: 'bottom-start' },

    // https://popper.js.org/docs/v2/modifiers/offset/
    offsetX: { type: Number, default: 0 },
    offsetY: { type: Number, default: 0 }
  },
  data () {
    return {
      shown: !!this.active,
      popper: null,
      timeout: null
    }
  },
  watch: {
    active (v) {
      this.shown = !!v
      this.toggle(this.shown)
    },
    placement (placement) {
      this.popper &&
        this.popper.setOptions({
          placement
        })
    }
  },
  beforeDestroy () {
    if (this.popper) {
      this.popper.destroy()
      document.body.removeChild(this.$refs.body)
    }
  },
  mounted () {
    this.mount()
  },
  methods: {
    mount () {
      if (this.$refs.activator && this.$refs.body && !this.popper) {
        document.body.appendChild(this.$refs.body)

        const modifiers = [
          {
            name: 'offset',
            options: { offset: [this.offsetX, this.offsetY] }
          }
        ]

        this.popper = createPopper(this.$refs.activator, this.$refs.body, {
          placement: this.placement,
          modifiers
        })
      }
    },
    toggle (shown) {
      if (this.disabled || shown === this.shown) {
        return
      }

      if (!this.shown) {
        this.show()
      } else {
        this.hide()
      }
    },

    show () {
      setTimeout(async () => {
        this.shown = true
        this.$emit('active-change', true)
        if (this.popper) {
          await this.popper.setOptions({
            scroll: true,
            resize: true
          })

          this.popper.update()
        }
      })
    },

    hide () {
      this.popper &&
        this.popper.setOptions({
          scroll: false,
          resize: false
        })

      this.shown = false
      this.$emit('active-change', false)

      if (this.timeout) {
        clearTimeout(this.timeout)
        this.timeout = null
      }
    },

    onActivatorFocus (e) {
      if (this.showOnFocus) {
        // Focus event fire before toggle is called making the popover hide right after
        // it is shown. Thus need a timeout to make sure toggle is called first
        this.timeout = setTimeout(() => {
          this.show()
          this.timeout = null
        }, 200)
      }
    },

    onActivatorKeyDown (e) {
      switch (e.keyCode) {
        case 13: // Enter
        case 38: // Up
        case 40: // Down
          this.show()
          break
        case 9: // Tab
        case 27: // ESC
          this.hide()
          break
      }

      this.$emit('activator-keydown', e, { active: this.shown })
    },

    onBodyClick (e) {
      e.stopPropagation()
    }
  }
}
</script>

<style lang="postcss">
.popover-body {
  & *::-webkit-scrollbar {
    width: 6px;
  }

  & *::-webkit-scrollbar-thumb {
    border-radius: 6px;
    background-color: transparent;
  }

  & *:hover {
    &::-webkit-scrollbar-thumb {
      background-color: rgb(221, 221, 221);
      &:hover {
        background-color: #999;
      }
    }
  }
}
</style>

<style lang="postcss" scoped>
.fade-enter,
.fade-leave-to {
  @apply opacity-0;
}

.fade-enter-active,
.fade-leave-active {
  @apply transition-opacity duration-150;
}
</style>
