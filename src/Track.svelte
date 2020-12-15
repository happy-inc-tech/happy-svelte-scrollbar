<script>
  import Thumb from './Thumb.svelte';
  import ScrollButton from './ScrollButton.svelte';

  export let className = 'scrollbar-track';
  export let wrapperElem = null;
  export let observerTarget = null;
  export let buttonPressingMove = 5;
  export let showArrows;

  let trackElem;
  let clickLock = false;
  let smooth = false;
  let pressingUp = false;
  let pressingDown = false;

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
      {#if showArrows}
        <ScrollButton direction="up" bind:pressing="{pressingUp}" />
      {/if}
      <Thumb
        wrapperElem="{wrapperElem}"
        trackElem="{trackElem}"
        observerTarget="{observerTarget}"
        smooth="{smooth}"
        on:lock-click="{() => (clickLock = true)}"
        on:unlock-click="{() => (clickLock = false)}"
        pressingUp="{pressingUp}"
        pressingDown="{pressingDown}"
        buttonPressingMove="{buttonPressingMove}"
        showArrows="{showArrows}"
      />
      {#if showArrows}
        <ScrollButton direction="down" bind:pressing="{pressingDown}" />
      {/if}
    {/if}
  </div>
{/if}

<style>
  .happy-scroll-track {
    position: absolute;
    z-index: 20;
  }

  .scrollbar-track {
    right: 0px;
    width: 13px;
    border-radius: 5px;
    box-shadow: 0 0 0 4px #dbdbdb;
    background-color: #dbdbdb;
  }
</style>
