<script>
  import { onMount, onDestroy, createEventDispatcher } from 'svelte';
  import { createObserver } from './utils/observer';

  export let className = 'scrollbar-thumb';
  export let wrapperElem = null;
  export let observerTarget = null;
  export let smooth = false;
  export let trackElem = null;

  const dispatch = createEventDispatcher();

  let thumbElem;
  let thumbHeight = '0px';
  let thumbTop = '0px';
  let noScroll = true;
  let pos = { top: 0, y: 0 };
  let unsubscribeObserver;

  /**
   * Calculating and setting new Height and Top of thumb
   */
  function calcThumbHeight() {
    const maxHeight = wrapperElem.scrollHeight;
    const visibleArea = wrapperElem.offsetHeight;
    const currentScrolled = wrapperElem.scrollTop;
    const visiblePercent = (visibleArea / maxHeight) * 100;
    const scrolledPercent = (currentScrolled / maxHeight) * 100;
    thumbTop = (visibleArea / 100) * scrolledPercent + 'px';
    thumbHeight = visiblePercent + '%';
    noScroll = thumbHeight === '100%';
  }

  /**
   * Common handler for "touchmove" and "mousemove"
   * @param {MouseEvent|TouchEvent} e
   */
  function thumbInteractionHappening(e) {
    e.preventDefault();
    e.stopPropagation();
    const clientY = e.type === 'touchmove' ? e.changedTouches[0].clientY : e.clientY;
    const dy = clientY - pos.y;
    wrapperElem.scrollTop = pos.top + dy;
  }

  /**
   * Common handler for "touchend" and "mouseup"
   */
  function thumbInteractionEnd() {
    setTimeout(() => {
      dispatch('unlock-click');
    });
    document.body.style.userSelect = 'inherit';
    document.removeEventListener('mousemove', thumbInteractionHappening);
    document.removeEventListener('mouseup', thumbInteractionEnd);
    document.removeEventListener('touchmove', thumbInteractionHappening);
    document.removeEventListener('touchend', thumbInteractionEnd);
  }

  /**
   * Common handler from "touchstart" and "mousedown
   * @param {MouseEvent|TouchEvent} e
   */
  function thumbInteractionStart(e) {
    dispatch('lock-click');
    e.preventDefault();
    e.stopPropagation();
    pos = {
      top: wrapperElem.scrollTop,
      y: e.type === 'touchstart' ? e.changedTouches[0].clientY : e.clientY
    };
    document.body.style.userSelect = 'none';
    if (e.type === 'touchstart') {
      document.addEventListener('touchmove', thumbInteractionHappening);
      document.addEventListener('touchend', thumbInteractionEnd);
    } else {
      document.addEventListener('mousemove', thumbInteractionHappening);
      document.addEventListener('mouseup', thumbInteractionEnd);
    }
  }

  /**
   * Setting position and size for
   * track element
   */
  function initTrackBar() {
    trackElem.style.top = wrapperElem.offsetTop + 'px';
    trackElem.style.height = wrapperElem.offsetHeight + 'px';
  }

  onMount(() => {
    initTrackBar();
    thumbElem.addEventListener('mousedown', thumbInteractionStart);
    thumbElem.addEventListener('touchstart', thumbInteractionStart);
    wrapperElem.addEventListener('scroll', calcThumbHeight);
    window.addEventListener('resize', initTrackBar);

    let observerElem = observerTarget.$$ ? observerTarget.happyObserverTarget() : observerTarget;
    unsubscribeObserver = createObserver(observerElem, () => calcThumbHeight());
  });

  onDestroy(() => {
    thumbElem.removeEventListener('mousedown', thumbInteractionStart);
    thumbElem.removeEventListener('touchstart', thumbInteractionStart);
    window.removeEventListener('resize', initTrackBar);
    unsubscribeObserver();
  });
</script>

<div
  bind:this="{thumbElem}"
  style="height: {thumbHeight}; top: {thumbTop}"
  class:happy-scroll-thumb="{true}"
  class="{className}"
  class:no-scroll="{noScroll}"
  class:smooth
></div>

<style>
  .scrollbar-thumb {
    background-color: #7cb86f;
    width: 27px;
    border-radius: 20px;
    cursor: pointer;
  }

  .happy-scroll-thumb {
    position: absolute;
    z-index: 21;
    display: block;
  }

  .happy-scroll-thumb.no-scroll {
    display: none;
  }

  .happy-scroll-thumb.smooth {
    transition: top 0.2s ease;
  }
</style>
