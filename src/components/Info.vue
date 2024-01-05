<template>
  <div class="info-links">
      <div class="icon">
        <span class="chevron"></span>
      </div>
      <div v-for="link in this.info" :key="link.link">
          <a 
            :href="link.link"
            target="_blank"
            rel=”noreferrer”
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

<style> 
.chevron::before {
	border-style: solid;
	border-width: 0.25em 0.25em 0 0;
	content: '';
	display: inline-block;
	height: 0.45em;
	left: 0.15em;
	position: relative;
	top: 0.15em;
	transform: rotate(-45deg);
	vertical-align: top;
	width: 0.45em;
}

.chevron.right:before {
	left: 0;
	/* transform: rotate(45deg); */
}

.icon {
  --iconDim: 70px;

  height: var(--iconDim);
  width: var(--iconDim);
  border-radius: 50%;
  background-color: var(--overlay, rgba(0,0,0,0.9));
  position: absolute;
  left: -3rem;
  top: 25px;
  box-shadow: 0px 0px 10px 5px rgba(0,0,0,0.3);
  display: flex;
  justify-content: center;
  align-items: center;
  transform: rotate(90deg);
  font-size: 160%;
}

.icon span {
  color: var(--base, white);
  margin-top: 12px;
  margin-left: -10px;
}
</style>
