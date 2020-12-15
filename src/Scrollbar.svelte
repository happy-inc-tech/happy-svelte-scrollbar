<script>
  import Track from './Track.svelte';

  export let observerTarget;
  export let scrollArea;
  export let buttonPressingMove = 5;
  export let showArrows = false;
</script>

<Track
  wrapperElem="{scrollArea}"
  observerTarget="{observerTarget}"
  buttonPressingMove="{buttonPressingMove}"
  showArrows="{showArrows}"
/>
