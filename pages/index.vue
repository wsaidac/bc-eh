<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">bc-eh</h1>
      <p class="subtitle">
        profile id: {{ bcProfileId }} <br />
        interactions id: {{ bcInteractionId }} <br />
      </p>
      <div class="links">
        <a target="_blank" class="button--green" @click="bcClickHandler">
          register click event
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  components: {
    Logo
  },
  data() {
    return {
      res: 'init',
      bcProfileId: 'n/a',
      bcInteractionId: 'n/a'
    }
  },
  head() {
    return {
      script: [
        {
          src:
            '//cg.sb.blueconic.net/frontend/static/javascript/blueconic/blueconic.min.js',
          type: 'text/javascript'
        }
      ]
    }
  },
  mounted() {
    if (
      typeof window.blueConicClient !== 'undefined' &&
      typeof window.blueConicClient.event !== 'undefined' &&
      typeof window.blueConicClient.event.subscribe !== 'undefined'
    ) {
      this.bcSubscribeToEvents()
    } else {
      // Not yet loaded; wait for the "onBlueConicLoaded" event
      window.addEventListener(
        'onBlueConicLoaded',
        function() {
          // BlueConic is loaded, now we can do API things
          this.bcSubscribeToEvents()
        },
        false
      )
    }
  },
  methods: {
    bcSubscribeToEvents() {
      window.blueConicClient.event.subscribe(
        window.blueConicClient.event.onBeforeInteractions,
        {},
        () => {
          // BlueConic prelisteners are about to be executed
          this.bcGetInteractionId()
          this.bcGetProfileId()
          this.bcViewHandler()
        }
      )
    },
    bcGetProfileId() {
      this.bcProfileId = window.blueConicClient.profile.getProfile().getId()
    },
    bcGetInteractionId() {
      const interactions = window.blueConicClient.getInteractions()

      if (interactions.length > 0) {
        const variantId = interactions[0].id
        const interactionId = window.blueConicClient.getInteractionNamesById(
          variantId
        ).id

        this.bcInteractionId = interactionId
      }
    },
    bcClickHandler() {
      if (!window.blueConicClient && !this.bcInteractionId) return undefined
      window.blueConicClient.createEvent('CLICK', this.bcInteractionId)
    },
    bcViewHandler() {
      if (!window.blueConicClient && !this.bcInteractionId) return undefined
      window.blueConicClient.createEvent('VIEW', this.bcInteractionId)
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
