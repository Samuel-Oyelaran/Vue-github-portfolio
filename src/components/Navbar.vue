<script setup>
import gitHubIcon from './icons/gitHubIcon.vue';
</script>

<template>

  <nav>
    <div class="navbar">
      <gitHubIcon class="github-icon" />
      <h1>SamuelOyelaran's Github</h1>

    </div>

  </nav>

</template>

<style scoped>


.navbar {
  display: flex;
  /* align-items: center; */
  gap: 10px;
  /* justify-content: center; */
  /* background-color: #393f4d; */
  font-family: 'Roboto', sans-serif;
  color: #dcddce;
  text-align: left;
  padding: 20px;
  margin-bottom: 100px;
}

.navbar h1 {
  transition: transform 0.3s ease;
}

.navbar h1:hover {
  transform: skewX(5deg);
  color: #dadede;
}



.github-icon {
  font-size: 3rem;
}
</style>
