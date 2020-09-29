<script>
  import Thumb from './Thumb.svelte';

  export let className = 'scrollbar-track';
  export let wrapperElem = null;
  export let observerTarget = null;

  let trackElem;
  let clickLock = false;
  let smooth = false;

  /**
   * Scrolling area when clicking on
   * scrollbar track
   *
   * @param {MouseEvent} event
   */
  function clickTrack(event) {
    if (clickLock) return;
    smooth = true;
    event.preventDefault();
    event.stopPropagation();
    const { offsetY } = event;
    const percents = (offsetY / trackElem.offsetHeight) * 100;
    wrapperElem.scrollTop = (wrapperElem.scrollHeight / 100) * percents;
    setTimeout(() => {
      smooth = false;
    }, 250);
  }
</script>

{#if wrapperElem}
  <div bind:this="{trackElem}" class="{className}" class:happy-scroll-track="{true}" on:click="{clickTrack}">
    {#if trackElem}
      <Thumb
        wrapperElem="{wrapperElem}"
        trackElem="{trackElem}"
        observerTarget="{observerTarget}"
        smooth="{smooth}"
        on:lock-click="{() => (clickLock = true)}"
        on:unlock-click="{() => (clickLock = false)}"
      />
    {/if}
  </div>
{/if}

<style>
  .happy-scroll-track {
    position: absolute;
    z-index: 20;
  }

  .scrollbar-track {
    right: 15px;
    width: 27px;
    border-radius: 20px;
    box-shadow: 0 0 0 4px #dbdbdb;
    background-color: #dbdbdb;
  }
</style>
