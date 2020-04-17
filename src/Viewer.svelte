<script>
  import Loader from "./Loader.svelte";
  import { createEventDispatcher, onMount, afterUpdate } from "svelte";
  import opensheetmusicdisplay from "opensheetmusicdisplay";
  import { writable } from "svelte/store";

  let title = "The Title";
  export let sheetUrl;
  let showMenu = true;
  let compact = true;
  let osmd;
  let loading = true;

  const dispatch = createEventDispatcher();

  onMount(async () => {
    compact = JSON.parse(localStorage.getItem("compact")) || false;
    osmd = new opensheetmusicdisplay.OpenSheetMusicDisplay("sheet-viewer", {
      drawingParameters: compact ? "compact" : "normal",
      drawPartNames: false,
      autoResize: false
    });
  });

  $: {
    if (osmd) {
      loading = true;
      osmd
        .load(sheetUrl)
        .then(() => {
          osmd.render();
          loading = false;
        })
        .catch(err => alert(err));
    }
  }

  function onViewModeChange() {
    compact = !compact;
    localStorage.setItem("compact", JSON.stringify(compact));
    osmd.setOptions({ drawingParameters: compact ? "compact" : "normal" });
    osmd.render();
  }
</script>

<style>
  .menu {
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 2rem;
    z-index: 500;
    background: #eee;
  }

  .border {
    border: 1px solid #555555;
  }

  #sheet-viewer {
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
    width: 100%;
  }

  .loader-wrapper {
    width: 100%;
    display: flex;
    justify-content: center;
    padding-top: 2rem;
    padding-bottom: 10rem;
    background: #fff;
  }

  .scroll-button {
    position: fixed;
    bottom: 1rem;
    right: 1rem;
    background: #777;
    appearance: none;
    border: #555;
    color: white;
    padding: 0.5rem;
    opacity: 0.9;
    cursor: pointer;
  }

  .scroll-button:hover {
    opacity: 1;
    background: #555;
  }
</style>

<main>
  {#if showMenu}
    <nav class="menu shadow-md">
      <button class="nav-button" on:click={() => (showMenu = false)}>
        <i class="material-icons ">close</i>
        <span class="button-text ">Close Menu</span>
      </button>
      <button
        class="nav-button border"
        class:active={compact}
        on:click={onViewModeChange}
        title={compact ? 'Switch compact view off' : 'Switch compact view on'}>
        {compact ? 'Compact view on' : 'Compact view off'}
      </button>
      <button class="nav-button" on:click={() => dispatch('showLibrary')}>
        <span class="button-text pl-1">Show Library&nbsp;</span>
        <i class="material-icons">library_music</i>
      </button>
    </nav>
  {:else}
    <button
      class="subtle-button shadow"
      title="Show Menu"
      on:click={() => (showMenu = true)}>
      <i class="material-icons">menu</i>
    </button>
  {/if}
  {#if loading}
    <div class="loader-wrapper">
      <Loader />
    </div>
  {/if}
  <button on:click={() => window.scrollBy(0, window.innerHeight * 0.8)} class="scroll-button shadow" title="scroll down">
  <i class="material-icons">arrow_downward</i>
  </button>
  <div id="sheet-viewer" />
</main>
