<script>
  import Track from './Track.svelte';

  export let observerTarget;
  export let scrollArea;
</script>

<Track wrapperElem="{scrollArea}" observerTarget="{observerTarget}" />
