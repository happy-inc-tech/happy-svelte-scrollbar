<script>
  export let direction = 'up';
  export let pressing = false;

  let button;

  function stopPressing() {
    pressing = false;
    document.removeEventListener('mouseup', stopPressing);
    document.removeEventListener('touchend', stopPressing);
  }

  function startPressing() {
    pressing = true;
    document.addEventListener('mouseup', stopPressing);
    document.addEventListener('touchend', stopPressing);
  }

  function click(evt) {
    evt.preventDefault();
    evt.stopPropagation();
  }
</script>

<div
  bind:this="{button}"
  class="scroll-button {direction}"
  on:click="{click}"
  on:mousedown="{startPressing}"
  on:touchstart="{startPressing}"
></div>

<style>
  .scroll-button {
    width: 0;
    height: 0;
    border-style: solid;
    position: relative;
    left: 50%;
    transform: translateX(-50%);
    cursor: pointer;
  }

  .scroll-button.up {
    top: -20px;
    border-width: 0 7.5px 10px 7.5px;
    border-color: transparent transparent #909091 transparent;
    line-height: 0px;
    _border-color: #000000 #000000 #909091 #000000;
    _filter: progid:DXImageTransform.Microsoft.Chroma(color='#000000');
  }

  .scroll-button.down {
    top: 100%;
    border-width: 10px 7.5px 0 7.5px;
    border-color: #909091 transparent transparent transparent;
    line-height: 0px;
    _border-color: #909091 #000000 #000000 #000000;
    _filter: progid:DXImageTransform.Microsoft.Chroma(color='#000000');
  }
</style>
