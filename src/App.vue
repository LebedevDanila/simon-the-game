<template>
  <div id="app">
    <h1 class="title">Simon Says</h1>

    <Game />
  </div>
</template>

<script>
import Game from '@/components/Game'

export default {
  name: 'App',
  components: {
    Game
  }
}
</script>
