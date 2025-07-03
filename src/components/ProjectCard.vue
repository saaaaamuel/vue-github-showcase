<template>
    <div class="project-card" :class="{ 'contribution-card': isContribution }">
        <div class="card-header">
            <h3 class="project-title">
                <a :href="project.html_url" target="_blank" rel="noopener noreferrer">
                    {{ project.full_name || project.name }}
                </a>
            </h3>
            <div class="card-badges">
                <span class="owner-badge" v-if="!isContribution">
                    Owner
                </span>
                <span class="project-visibility" v-if="project.private">
                    Private
                </span>
                <span class="contribution-badge" v-if="isContribution">
                    Contributor
                </span>
            </div>
        </div>

        <p class="project-description" v-if="project.description">
            {{ truncateDescription(project.description) }}
        </p>
        <p class="project-description no-description" v-else>
            No description provided
        </p>

        <div class="project-meta">
            <span class="project-language" v-if="project.language">
                <span class="language-color" :style="{ backgroundColor: getLanguageColor(project.language) }"></span>
                <span class="language-text">{{ project.language }}</span>
            </span>
            <div class="project-stats">
                <span class="project-stars" title="Stars">
                    ⭐ {{ project.stargazers_count || 0 }}
                </span>
                <span class="project-forks" title="Forks">
                    🍴 {{ project.forks_count || 0 }}
                </span>
            </div>
        </div>

        <div class="project-footer">
            <span class="project-updated" v-if="project.updated_at">
                Updated {{ formatDate(project.updated_at) }}
            </span>
            <!-- <a :href="project.html_url" target="_blank" rel="noopener noreferrer" class="project-link">
                View on GitHub
            </a> -->
            <a v-if="project.has_pages" :href="`https://${project.owner.login}.github.io/${project.name}/`"
                target="_blank" rel="noopener noreferrer" class="project-link">
                View on Pages
            </a>
        </div>
    </div>
</template>

<script setup>
const props = defineProps({
    project: {
        type: Object,
        required: true
    },
    isContribution: {
        type: Boolean,
        default: false
    }
});

const truncateDescription = (desc) => {
    if (desc.length > 100) {
        return desc.substring(0, 100) + '...';
    }
    return desc;
};

const formatDate = (dateString) => {
    const date = new Date(dateString);
    return date.toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'short',
        day: 'numeric'
    });
};

const languageColors = {
    'JavaScript': '#f1e05a',
    'TypeScript': '#3178c6',
    'Python': '#3572A5',
    'Java': '#b07219',
    'C++': '#f34b7d',
    'C': '#555555',
    'Ruby': '#701516',
    'PHP': '#4F5D95',
    'CSS': '#563d7c',
    'HTML': '#e34c26',
    'Vue': '#41b883',
    'Shell': '#89e051',
    'Go': '#00ADD8',
    'Rust': '#dea584',
    'Swift': '#ffac45',
    'Kotlin': '#A97BFF',
    // Add more as needed
};

const getLanguageColor = (language) => {
    return languageColors[language] || '#cccccc';
};

</script>

<style scoped>
@import 'ProjectCard.css';
</style>