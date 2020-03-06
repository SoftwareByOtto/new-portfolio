<template>
  <div>
    <b-container>
      <h1>Otto's Portfolio</h1>
      <b-card-group deck>
        <div v-for="project in projects" :key="project.title">
          <b-card :title="project.title" :sub-title="project.description">
            <b-button :to="project.title">Go to project</b-button>
            <template v-slot:footer>
              <div v-for="tag in project.tags" :key="tag">
                <b-badge>{{tag}}</b-badge>
              </div>
            </template>
          </b-card>
        </div>
      </b-card-group>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      projects: [
        {
          title: "Countdown",
          description: "A game of wits",
          tags: ["algorithms", "stuff"]
        },
        { title: "Timer", description: "test", tags: [] },
        { title: "Thingy", description: "test", tags: [] },
        { title: "Stuff", description: "test", tags: [] }
      ]
    };
  },
  methods: {},
  computed: {}
};
</script>

<style scoped>
</style>