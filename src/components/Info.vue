<template>
  <div class="info">
      <div v-for="link in info" :key="link.link">
          <a :href="link.link">{{ link.title }}</a>
      </div>
  </div>
</template>

<script>
export default {
  name: 'Info',
  data() {
      return {
        info: [
            {
                title: 'Understanding Use of Color (WCAG)',
                link: 'https://www.w3.org/WAI/WCAG21/Understanding/use-of-color.html',
            },
            {
                title: 'About Color Contrast (MDN)',
                link: 'https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG/Perceivable/Color_contrast',
            },
            {
                title: 'About Use of Color/Perceivable (MDN)',
                link: 'https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG/Perceivable/Use_of_color',
            },
        ],
      }
  }
}
</script>

<style>

</style>
