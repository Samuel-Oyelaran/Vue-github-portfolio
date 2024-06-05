<script setup>
import PrevIcon from './icons/prevIcon.vue';
import NextIcon from './icons/nextIcon.vue';
import Skeleton from './Skeleton.vue';
</script>

<script>
export default {
  data() {
    return {
      repoData: [],
      currentPage: 1,
      loading: false,
      perPage: 6,
      skeleton: [...new Array(6)],
      selectedLanguage: '',
      searchTerm: '',
      manageRepoId: null, // Track which repo's manage options are visible
    };
  },
  methods: {
    fetchData() {
      this.loading = true;
      fetch(`https://api.github.com/users/Samuel-Oyelaran/repos`, {
        headers: {
          Accept: "application/json"
        },
      })
        .then((res) => res.json())
        .then((data) => {
          this.repoData = data;
          this.loading = false;
        });
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    nextPage() {
      if (this.currentPage < this.lastPage) {
        this.currentPage++;
      }
    },
    handleLanguageFilter(language) {
      this.selectedLanguage = language;
      this.currentPage = 1;
    },
    toggleManageOptions(repoId) {
      this.manageRepoId = this.manageRepoId === repoId ? null : repoId;
    },
    handleCancel() {
      this.manageRepoId = null;
    },
    handleDelete(repoId) {
      // Logic to handle delete (e.g., API call to delete repo)
      console.log(`Deleting repo with ID: ${repoId}`);
      this.manageRepoId = null;
    }
  },
  mounted() {
    this.fetchData();
  },
  computed: {
    showMore() {
      const start = (this.currentPage - 1) * this.perPage;
      const end = start + this.perPage;
      return this.repoData.filter(repo => {
        if (this.selectedLanguage && repo.language !== this.selectedLanguage) {
          return false;
        }
        if (this.searchTerm && !repo.name.toLowerCase().includes(this.searchTerm.toLowerCase())) {
          return false;
        }
        return true;
      }).slice(start, end);
    },
    lastPage() {
      return Math.ceil(this.repoData.length / this.perPage);
    }
  }
}
</script>

<template>
  <div class="welcomeMessage">
    <h2>
      Heyy!👋 <span class="welcome">Welcome</span>
    </h2>
  </div>
  <div>
    <div class="main-inputs">
      <input class="search-input" type="text" placeholder="Search repos here..." v-model="searchTerm">
      <i class="fas fa-search"></i>
      <select class="select-btn" v-model="selectedLanguage" @change="handleLanguageFilter(selectedLanguage)">
        <option value="">Filter</option>
        <option value="HTML">HTML</option>
        <option value="CSS">CSS</option>
        <option value="JavaScript">JavaScript</option>
      </select>
    </div>
    <div class="repoTitle">
      <h1>My Repositories</h1>
    </div>
    <div class="repo-container">
      <Skeleton v-if="loading" v-for="n in skeleton" :key="n">{{ skeleton }}</Skeleton>
      <div v-else v-for="repo in showMore" class="repo-card" :key="repo.id">
        <router-link :to="`/details/${repo.name}`">
          <h2 class="repo-name">{{ repo.name }}</h2>
        </router-link>
        <p class="language">Language: {{ repo.language }}</p>
        <p class="date">Start date & time: {{ repo.created_at }}</p>
        <p class="visibility">Visibility: {{ repo.visibility }}</p>
        <!-- <div class="manage-btn">
          <button @click="toggleManageOptions(repo.id)">Manage</button>
          <div v-if="manageRepoId === repo.id" class="manage-options">
            <button @click="handleDelete(repo.id)">Delete</button>
            <button @click="handleCancel(repo.id)">Cancel</button>
          </div>
        </div> -->
      </div>
    </div>
    <div class="pagination">
      <button class="view-more" :class="currentPage === 1 ? 'disabled' : ''" @click="prevPage">
        <PrevIcon />
      </button>
      <p class="current-page">{{ currentPage }}</p>
      <button class="view-more" :class="currentPage === lastPage ? 'disabled' : ''" @click="nextPage">
        <NextIcon />
      </button>
    </div>
  </div>
  <footer class="foot">
    <div class="social-links">
      <a href="#">
        <svg class="social-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
          <path d="M24 4.56c-.89.39-1.84.66-2.84.78a4.92 4.92 0 002.16-2.72 9.84 9.84 0 01-3.1 1.18 4.92 4.92 0 00-8.38 4.48A13.93 13.93 0 011.67 3.15 4.93 4.93 0 003.18 9.72a4.91 4.91 0 01-2.23-.62v.06a4.93 4.93 0 003.95 4.83 4.92 4.92 0 01-2.22.08 4.93 4.93 0 004.59 3.42A9.87 9.87 0 010 21.54a13.91 13.91 0 007.55 2.21c9.06 0 14.01-7.5 14.01-14 0-.21 0-.43-.02-.64a10.01 10.01 0 002.47-2.54z" />
        </svg>
      </a>
      <a href="#">
        <svg class="social-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
          <path d="M22.23 0H1.77A1.76 1.76 0 000 1.76v20.48A1.76 1.76 0 001.77 24h20.48A1.76 1.76 0 0024 22.24V1.76A1.76 1.76 0 0022.23 0zM7.12 20.45H3.54V9h3.58v11.45zM5.33 7.62a2.07 2.07 0 01-2.1-2.08 2.08 2.08 0 112.1 2.08zm14.72 12.83h-3.58v-5.5c0-1.31-.02-3-1.82-3s-2.1 1.42-2.1 2.91v5.59H9.97V9h3.43v1.56h.05a3.77 3.77 0 013.39-1.87c3.62 0 4.29 2.38 4.29 5.47v6.29z" />
        </svg>
      </a>
    </div>
    <p>© 2024 Samuel's Portfolio</p>
  </footer>
</template>

<style>

.manage-btn {
  display: flex;
  justify-content: center;
  /* margin-top: 10px; */
  width: 100%;
  height: 40px;
  background-color: #166771;
  border-radius: 10px;
  margin-top: 10px;
  /* position: relative;
  bottom: 0; */
  align-self: end;
}

.manage-btn button {
  color: #cbc8c8;
  font-size: 16px;
}

.manage-btn:hover {
  background-color: #187581;
}

.social-links {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

.social-icon {
  width: 20px;
  height: 20px;
  fill: #eee8d3;
  /* fill: currentColor; */
}


.welcomeMessage h2 {
  font-size: 2rem;
  font-style: italic;
  color: #dbe1a2;
  justify-content: center;
  text-align: center;
  margin-bottom: 50px;
  font-family: "Major Mono Display", monospace;
  overflow: hidden;
  white-space: nowrap;
  animation: typewriter 5s steps(40) forwards;
  /* Apply typewriter animation */
}

.welcome {
  color: #ddd6bc;
}

/* Animation for typewriter effect */
@keyframes typewriter {
  from {
    width: 0;
    /* Start with no width */
  }

  to {
    width: 100%;
    /* Increase width to full length */
  }
}




.search-input {
  width: 50%;
  font-size: 15px;
  height: 30px;
  padding: 17px;
  border: 1.3px solid #4a5779;
  border-radius: 10px;
  color: #8ba1d6;
  background-color: #1d1e22;
  font-weight: 750;
  align-items: center;
  justify-content: center;
  margin-left: 8%;
}

i  {
  color: #adb8d3;
  font-size: 15px;
  margin-left: -22px;
  margin-top: 5px;
  position: relative;
  z-index: 2;
}


.search-input:focus {
  outline: none;
  /* Remove the default outline */
  box-shadow: 0 0 5px 2px #9ddfe8;
  /* Blue shadow */
}

.select-btn {
  font-size: 15px;

  margin-top: 10px;

  height: 29px;
  padding: 5px;
  border: none;
  border-radius: 5px;
  color: #191c23;
  background-color: #1b9aaa;
  font-weight: 760;
  margin-left: 20px;
}

.select option {
  color: #f5f1e3;
}

.select-btn:hover {
  background-color: #36b4c5;
}


.repoTitle {
  font-size: 1.2rem;
  font-style: italic;
  color: #dcddce;
  justify-content: center;
  text-align: center;
  margin-bottom: 50px;
  font-family: "Major Mono Display", monospace;
  overflow: hidden;
  white-space: nowrap;
  padding-top: 3rem;
  /* animation: typewriter 3s steps(40), fadeOut 1s 3s forwards; Apply both animations */
}

.language,
.date,
.visibility {
  color: #8ba1d6;
  /* font-weight: 510; */
  font: 20px;
  font-family: "Major Mono Display", monospace;
}

.repo-container {
  display: grid;
  /* grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); */
  grid-gap: 30px;
  width: 90%;
  margin: 0 auto;
  margin-bottom: 5rem;
}


@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.repo-card {
  animation: fadeIn 1.5s ease-out forwards;
  transition: transform 0.3s ease, box-shadow 0.3s ease;

}

.repo-card,
.repodetail-card {
  border: 1px solid #1b9aaa;
  padding: 15px;
  border-radius: 15px;
  box-shadow: #7c8db5;
 
}



.repo-card:hover {
  transform: rotate(1deg);
  box-shadow: 0 0 10px #9ddfe8;
}

a {
  text-decoration: none;
  color: #0d7591;
}

a:hover {
  text-decoration: underline;
}

.repo-name {
  color: #0e5f76;
  font-size: 2rem;
  word-break: break-word;
}

p {
  padding: 5px 0;
}

.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin: 3rem 0;
}

button {
  background: none;
  border: none;
  cursor: pointer;
}

.view-more svg {
  width: 2rem;
}

.disabled {
  cursor: not-allowed;
  opacity: 0.5;
}

.foot {
  color: #f5f1e3;
  text-align: center;
  padding: 20px;
  position: relative;
  bottom: 10px;
  width: 100%;
  font-size: 15px;
}


.manage-options {
  position: absolute;
  top: 100%;
  right: 0;
  background: #716b6b;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  gap: 1px;
  z-index: 1;
  width: 100px;
  /* Hide the manage options by default */
  display: none;
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

  /* .input {
    width: 60%;
    margin-left: 20px;
  } */
}
</style>