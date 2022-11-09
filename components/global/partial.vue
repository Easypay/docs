<template>
  <div>
    <nuxt-content :document="content" />
  </div>
</template>

<script>
export default {
  props: {
    path: {
      type: String,
      required: true,
    }
  },
  data: () => ({
    content: []
  }),
  async fetch() {
    this.content = await this.$content(this.path).fetch()
  }
}
</script>
