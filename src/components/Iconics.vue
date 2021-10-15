<template>
  <svg
    v-if="icon === 'loading'"
    style="width:30px;height:30px"
    viewBox="0 0 30 30"
    class="rotating"
  >
    <path fill="currentColor" d="M12,4V2A10,10 0 0,0 2,12H4A8,8 0 0,1 12,4Z" />
  </svg>

  <svg
    v-else-if="icon === 'add'"
    style="width:24px;height:24px"
    viewBox="0 0 24 24"
  >
    <path
      fill="currentColor"
      d="M12,20C7.59,20 4,16.41 4,12C4,7.59 7.59,4 12,4C16.41,4 20,7.59 20,12C20,16.41 16.41,20 12,20M12,2A10,10 0 0,0 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2M13,7H11V11H7V13H11V17H13V13H17V11H13V7Z"
    />
  </svg>

  <svg
    v-else-if="icon === 'cancel'"
    style="width:24px;height:24px"
    viewBox="0 0 24 24"
  >
    <path
      fill="currentColor"
      d="M12,20C7.59,20 4,16.41 4,12C4,7.59 7.59,4 12,4C16.41,4 20,7.59 20,12C20,16.41 16.41,20 12,20M12,2C6.47,2 2,6.47 2,12C2,17.53 6.47,22 12,22C17.53,22 22,17.53 22,12C22,6.47 17.53,2 12,2M14.59,8L12,10.59L9.41,8L8,9.41L10.59,12L8,14.59L9.41,16L12,13.41L14.59,16L16,14.59L13.41,12L16,9.41L14.59,8Z"
    />
  </svg>
</template>

<script>
export default {
  name: "Iconics",
  props: {
    icon: {
      type: String,
      default: "",
    },

    rotating: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<style lang="scss" scoped>
svg {
  &.rotating {
    animation: spin 1s linear infinite;
  }
}

@-moz-keyframes spin {
  100% {
    -moz-transform: rotate(360deg);
  }
}
@-webkit-keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
  }
}
@keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
</style>
