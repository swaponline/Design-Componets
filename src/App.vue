<template>
  <v-app id="app">
    <media-query-provider :queries="queries">
      <router-view />
    </media-query-provider>
  </v-app>
</template>

<script>
import { MediaQueryProvider } from 'vue-component-media-queries'
import { MODULE_NAME as PROFILE_MODULE } from '@/store/modules/Profile'
import messageHandler from './messageHandler'

const queries = {
  desktop: '(min-width: 1281px)',
  tablet: '(max-width: 1280px)',
  phone: '(max-width: 480px)',
  small: '(max-width: 320px)'
}
export default {
  name: 'App',
  components: {
    MediaQueryProvider
  },
  data() {
    return {
      queries
    }
  },
  computed: {
    model() {
      return this.$store.state[PROFILE_MODULE].model
    },
    background() {
      return this.model.background
    },
    color() {
      return this.model.color
    }
  },
  watch: {
    background: {
      immediate: true,
      handler(background, oldBackground) {
        document.documentElement.style.setProperty('--prev-background-app', oldBackground)

        const { classList } = document.getElementById('app')

        classList.add(`background-app-after`)
        document.documentElement.style.setProperty('--background-app', background)

        setTimeout(() => {
          for (let i = 1; i < classList.length; i += 1) {
            if (classList[i].includes('-after')) classList.remove(classList[i])
          }
        }, 400)
      }
    },
    color: {
      immediate: true,
      handler(val) {
        document.documentElement.style.setProperty('--main-color', val)
      }
    }
  },
  mounted() {
    window.addEventListener('resize', this.resize)
    this.resize()
    messageHandler()
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resize)
  },
  methods: {
    resize() {
      let vh
      if (window.innerWidth > 1920) {
        // Правим высоту, чтобы было на весь экран без скролла
        // По сути делим на то число, на которе зумим в css
        vh = (window.innerHeight * 0.01) / ((window.innerWidth * 0.01) / 18)
      } else {
        vh = window.innerHeight * 0.01
      }
      document.documentElement.style.setProperty('--vh', `${vh}px`)

      const vw = window.innerWidth * 0.01
      document.documentElement.style.setProperty('--vw', `${vw}`)
    }
  }
}
</script>

<style lang="scss">
#app {
  position: relative;
  height: calc(var(--vh, 1vh) * 100);
  width: 100%;
  max-width: 100vw;
  overflow: hidden;
  background-color: transparent;

  &::before {
    content: '';
    position: absolute;
    left: -5%;
    top: -5%;
    height: 110%;
    width: 110%;
    opacity: 0.85;
    background-image: var(--background-app);
    background-size: 100% 100%;
    z-index: -2;
  }

  &::after {
    content: '';
    position: absolute;
    top: 110%;
    left: -5%;
    height: 0;
    width: 0;
    opacity: 0.85;
    background-size: 100% 100%;
    z-index: -1;
  }

  @include small-height {
    min-height: calc(var(--vh, 1vh) * 100);
    height: 100%;
  }
}

#app.background-app-after {
  &::before {
    background-image: var(--prev-background-app);
  }

  &::after {
    top: -5%;
    width: 110%;
    height: 110%;
    background-image: var(--background-app);
    transform-origin: center center;
    opacity: 0.85;
    transition: 0.4s;
  }
}

@media screen and (min-width: 1921px) {
  #app {
    zoom: calc(var(--vw) / 18);
  }
}
</style>
