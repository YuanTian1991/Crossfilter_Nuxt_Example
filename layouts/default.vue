<template>
  <v-app>
    <v-toolbar
      fixed
      flat
      app
      color="white"
      style="font-family: 'Courier New', monospace;"
    >
      <v-spacer />
      <v-btn
        icon
        href="https://yuantian1991.github.io/"
      >
        <v-avatar
          :size="40"
          color="white lighten-4"
        >
          <img
            src="~/assets/image/QQ.jpg"
            alt="avatar"
          >
        </v-avatar>
      </v-btn>
      <v-toolbar-title class="hidden-sm-and-down">
        <a
          id="title"
          href="https://yuantian1991.github.io/"
        > {{ title }} </a>
      </v-toolbar-title>

      <v-spacer />
      <v-toolbar-title />
      <v-spacer />
    </v-toolbar>

    <v-content class="white">
      <v-container>
        <nuxt />
      </v-container>
    </v-content>

    <Footer />
  </v-app>
</template>

<script>
import Footer from '~/components/Footer'

export default {
  components: {
    Footer: Footer
  },
  data() {
    return {
      fixed: true,
      items: [
        { icon: 'apps', title: 'Welcome', to: '/' },
        { icon: 'bubble_chart', title: 'Inspire', to: '/inspire' }
      ],
      title: 'Yuan Tian',
      WordsLink: [
        { title: 'Musings/随感', to: '/musing' },
        // { title: 'Articles/长文', to: '/article' },
        { title: 'Photography/摄影', to: '/photography' },
        { title: 'Reviews/书影游戏', to: '/review' }
      ],
      ProjectsLink: [
        { title: 'visualization/可视化', to: '/visualization' }
        // { title: 'projects/成品', to: '/project' }
      ]
    }
  }
}
</script>

<style>
#title {
  text-decoration: none;
}

#title:hover {
  font-weight: bolder;
}
</style>
