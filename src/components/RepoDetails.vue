<template>
  <div id="repodetail">
    <!-- <router-link :to="`/`"><button class="back"><BackIcon/> back</button></router-link> -->
    <DetailsSkeleton v-if="loading" />
    <div v-else class="repodetail-card">
      <h2 class="repo-name">{{ details.name }}</h2>
      <div class="repo-mini-details">
        <p><StarIcon /> Stars: {{ details.stargazers_count }}</p>
        <p><WatchIcon /> Watch: {{ details.watchers }}</p>
        <p><ForkIcon /> Forks: {{ details.forks }}</p>
        <p><BranchIcon /> Branches: {{ branches.length }}</p>
      </div>
      <p v-if="details.language === null">Main Language: none</p>
      <p v-else>Main Language: {{ details.language }}</p>
      <p v-if="deployments.length === 0">Live site: none</p>
      <p v-else>Error Page: <a :href="`https://Samuel-Oyelaran.github.io/${details.name}`">Okezedavid.github.io/{{ details.name }}</a></p>
      <p><a :href="`https://github.com/${details.full_name}`" target="_blank">View on Github</a></p>
    </div>
    <button class="home-button" @click="goHome">Home Page</button>
  </div>
</template>

<script>
import BackIcon from './icons/backIcon.vue';
import BranchIcon from './icons/branchIcon.vue';
import ForkIcon from './icons/forkIcon.vue';
import StarIcon from './icons/starIcon.vue';
import WatchIcon from './icons/watchIcon.vue';
import DetailsSkeleton from './DetailsSkeleton.vue';

export default {
  data() {
    return {
      details: [],
      branches: [],
      deployments: [],
      loading: false,
    };
  },
  methods: {
    fetchData: function () {
      this.loading = true;
      fetch(`https://api.github.com/repos/Samuel-Oyelaran/${this.$route.params.id}`, {
        headers: {
          Accept: 'application/json',
        },
      })
        .then((res) => res.json())
        .then((details) => (this.details = details));

      fetch(`https://api.github.com/repos/Samuel-Oyelaran/${this.$route.params.id}/branches`, {
        headers: {
          Accept: 'application/json',
        },
      })
        .then((res) => res.json())
        .then((branches) => (this.branches = branches));

      fetch(`https://api.github.com/repos/Samuel-Oyelaran/${this.$route.params.id}/deployments`, {
        headers: {
          Accept: 'application/json',
        },
      })
        .then((res) => res.json())
        .then((deployments) => {
          this.deployments = deployments;
          this.loading = false;
        });
    },
    goHome() {
      this.$router.push('/');
    },
  },
  mounted() {
    this.fetchData();
  },
  components: { StarIcon, WatchIcon, ForkIcon, BranchIcon, BackIcon, DetailsSkeleton },
};
</script>

<style>
/* Your existing styles */
.repo-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-gap: 30px;
  width: 90%;
  margin: 0 auto;
  margin-bottom: 5rem;
}

.repo-card,
.repodetail-card {
  border: 1px solid #1b9aaa;
  padding: 15px;
  border-radius: 15px;
  box-shadow: #7c8db5;
}

.repodetail-card {
  background-color: #2e3333;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.repodetail-card:hover {
  transform: rotate(1deg);
  box-shadow: 0 0 10px #9ddfe8;
}



.repo-name {
  color: #0e5f76;
  font-size: 2rem;
  word-break: break-word;
}

#repodetail {
  width: 90%;
  max-width: 600px;
  margin: 0 auto;
  margin-bottom: 50px;
}

#repodetail p {
  padding: 15px 0;
  word-break: break-all;
}

.repo-mini-details {
  gap: 10px 50px;
  margin: 0 0 15px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
}

.repo-mini-details p {
  display: flex;
  align-items: center;
  gap: 0.3rem;
}

.icons {
  color: #1b9aaa;
}
.back {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 2rem;
  color: #1b9aaa;
}

.home-button {
  background-color: #1b9aaa;
  color: #f5f1e3;
  padding: 8px 18px;
  border: none;
  width: 100%;
  border-radius: 8px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 600;
  max-width: fit-content;
  margin: 10px auto;
  display: block;
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
}

.home-button:hover {
  background-color: #36b4c5;
}

@media screen and (max-width: 480px) {
  .repo-container {
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  }

  .repo-name {
    font-size: 20px;
  }

  h1 {
    font-size: 1.5rem;
  }

  .github-icon {
    font-size: 2rem;
  }
  .input {
    width: 55%;
    margin-left: 23px;
  }
}
</style>
