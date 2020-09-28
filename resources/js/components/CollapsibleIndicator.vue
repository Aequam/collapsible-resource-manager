<template>
  <div
    class="absolute flex flex-auto collapsible-indicator"
    :class="style"
    v-if="visible"
  >
    <svg
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 24 24"
      width="24"
      height="24"
    >
      <path
        v-if="expanded"
        fill="currentColor"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="2"
        d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
      />
      <path
        v-else
        fill="currentColor"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="2"
        d="M12.95 10.707l.707-.707L8 4.343 6.586 5.757 10.828 10l-4.242 4.243L8 15.657l4.95-4.95z"
      />
    </svg>
  </div>
</template>

<script>
export default {
  name: "CollapsibleIndicator",
  props: {
    expanded: {
      default: true,
    },
    visible: {
      default: false,
    },
    style: {
      default: () => ({}),
    },
  },
};
</script>

<style>
.collapsible-indicator {
  top: 2px;
  right: 0;
}

h4 .collapsible-indicator {
  top: 2px;
}
</style>
