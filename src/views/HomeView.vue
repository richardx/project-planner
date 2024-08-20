<template>
  <div class="home">
    <FilterNav @filterChange="current = $event" :current="current" />
    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject :project="project" @delete="handleDelete" @complete="handleComplete" />
      </div>
    </div>
  </div>
</template>

<script>
import SingleProject from '../components/SingleProject.vue'
import FilterNav from '../components/FilterNav.vue'

export default {
  name: 'HomeView',
  components: { SingleProject, FilterNav },
  data() {
    return {
      projects: [],
      current: 'all',
    };
  },
  mounted() {
    fetch('http://localhost:3000/projects') // denne fetcher fra vores API
      .then(response => response.json()) // vi konverterer responsen til JSON
      .then(data => this.projects = data) // vi gemmer data i vores projects array
      .catch(error => console.log(error.message)) // vi håndterer fejl
  },
  methods: {
    handleDelete(id) {
      this.projects = this.projects.filter((project) => { // vi filtrerer vores projects array
        return project.id !== id // vi returnerer alle projekter, der ikke har den givne id
      })
    },
    handleComplete(id) {
      let p = this.projects.find(project => { // vi finder det projekt, der har den givne id
        return project.id === id // vi returnerer projektet, hvis id matcher
      })
      p.complete = !p.complete // vi inverterer complete værdien
    }
  },
  computed: {
    filteredProjects() {
      if (this.current === 'completed') {
        return this.projects.filter(project => project.complete)
      }
      if (this.current === 'ongoing') {
        return this.projects.filter(project =>!project.complete)
      }
      return this.projects
    }
  }
}
</script>
