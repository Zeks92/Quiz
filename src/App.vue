<template>
  <!-- starPower hack for proper animation display on style binding -->
  <div class="container" :style="starPower">
    <app-game-over-modal v-if="isGameOver"></app-game-over-modal>
  
  
    <main>
      <header :class="classHeader">
        <h1>Quiz</h1>
      </header>
  
      <!-- Primary page components -->
      <transition name="fade" mode="out-in">
        <component :is="currentView"></component>
        <!-- show element to test loading state -->
        <!-- <app-loader></app-loader> -->
      </transition>
  
    </main>
  </div>
</template>

<script>
import GameBoard from './components/GameBoard.vue';
import GameOverModal from './components/GameOverModal.vue';
import Intro from './components/Intro.vue';
import Loader from './components/Loader.vue';
export default {
  components: {
    'app-game-board': GameBoard,
    'app-game-over-modal': GameOverModal,
    'app-intro': Intro,
    'app-loader': Loader,
  },

  computed: {
    currentView() {
      return this.$store.state.currentView;
    },
    isGameOver() {
      return this.$store.state.isGameOver;
    },
    // Expand and contract header when changing game state
    classHeader() {
      let classHeader = {
        grow: this.currentView === 'app-intro',
        small: this.currentView !== 'app-intro',
      }
      return classHeader;
    },
    // Linked to StarPower.vue and starPower state
    starPower() {
      return this.$store.state.starPower ? 'overflow: hidden' : 'overflow: inherit';
    },
  },

  created() {
    // Fetch categories from open trivia using vue-resource, send to store
    this.$http.get('https://opentdb.com/api_category.php')
      .then(response => {
        return response.json();
      })
      .then(data => {
        this.$store.state.categories = data.trivia_categories;
      });
  }
}
</script>

<style lang="scss">
@import "main.scss";

$header-shrink-scale: .66;

body {
  background-color: #4481eb;
  background-image: $back-gradient;
  background-repeat: no-repeat;
  color: $color-white;
  display: flex;
  font-family: 'Quicksand', sans-serif;
  height: 100vh;
  justify-content: center;
  text-align: center;
}

h1,
p {
  margin: 0;
}

main {
  align-items: center;
  animation: fade-in 1.5s ease; // defined in main.scss
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  height: 100%;
}


.container {
  width: 100%;
}

.icon-menu,
.icon-close {
  transition: all .3s ease;

  &:hover {
    color: $color-med;
  }
}

.grow {
  animation: grow 0.5s ease forwards; //background-color: $color-dark;
}

.small {
  animation: squeeze 0.5s ease forwards; //background-color: $color-darkest;
}

@keyframes grow {
  from {
    transform: scale($header-shrink-scale);
    padding: 0.25em 0;
  }
  to {
    transform: scale(1);
    padding: 1em;
  }
}

@keyframes squeeze {
  from {
    transform: scale(1);
    padding: 1em;
  }
  to {
    transform: scale($header-shrink-scale);
    padding: 0.5em 0;
  }
}

.expand-enter-active {
  animation: open .75s ease forwards;
}

.expand-leave-active {
  animation: close .5s ease forwards;
}


@media (max-width: 600px) {}
</style>
