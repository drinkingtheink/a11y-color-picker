<template>
  <div class="info-links">
      <div v-for="link in this.info" :key="link.link">
          <a 
            :href="link.link"
            target="_blank"
            
        >{{ link.title }}</a>
      </div>
  </div>
</template>

<script>
export default {
  name: 'Info',
  props: {
    info: {
      type: Array
    },
  },
}
</script>
