<template>
  <div class="app">
    <header class="header">
      <h1>
        My <a :href="`https://github.com/${githubConfig.username}`" target="_blank" rel="noopener noreferrer"
          class="github-link">GitHub</a> Showcase
      </h1>
      <p>Projects I've created and contributed to</p>
    </header>

    <main class="main-content">
      <div v-if="loading" class="loading">Loading projects...</div>
      <div v-else-if="error" class="error">Error loading projects: {{ error }}</div>
      <div v-else class="projects-container">
        <div class="projects-grid">
          <ProjectCard v-for="project in projects" :key="project.id" :project="project"
            :is-contribution="isContribution(project)" />
        </div>
      </div>
    </main>

    <footer class="footer">
      <p> Made by <a :href="`https://github.com/saaaaamuel`" target="_blank" rel="noopener noreferrer"
          class="github-link">Samuel</a> with ❤️</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';
import ProjectCard from './components/ProjectCard.vue';
import { githubConfig } from './config';

const loading = ref(true);
const error = ref(null);
const projects = ref([]);

onMounted(async () => {
  try {
    // Fetch user's own repositories

    // type (string): Can be one of all, owner, member. Default: owner
    // sort (string): Can be one of created, updated, pushed, full_name. Default: full_name
    // direction (string): Can be one of asc or desc. Default: asc when using full_name, otherwise desc
    // page (integer): Current page
    // per_page (integer): number of records per page

    const type = githubConfig.includeContributions ? 'all' : 'member';

    const repos = await axios.get(
      `https://api.github.com/users/${githubConfig.username}/repos?sort=updated&type=${type}&direction=desc&per_page=${githubConfig.maxRepos}`
    );
    projects.value = filterRepos(repos.data);
  } catch (err) {
    error.value = err.message;
    console.error('Error fetching GitHub data:', err);
  } finally {
    loading.value = false;
  }
});

function filterRepos(repos) {
  return repos.filter(repo =>
    !githubConfig.excludedRepos.includes(repo.name) &&
    !repo.fork // Optionally exclude forks
  );
}

function isContribution(project) {
  return project.owner.login !== githubConfig.username;
};
</script>

<style>
@import 'App.css';
</style>