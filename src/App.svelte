<script>
  import Viewer from "./Viewer.svelte";
  import Library from "./Library.svelte";
  import { slide } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";
  import { onMount } from "svelte";

  let sheetUrl;
  let showLibrary = false;
  // let showLibrary = true;

  onMount(() => {
    sheetUrl = localStorage.getItem("lastSheet") || null;
    if (sheetUrl === null) {
      showLibrary = true;
    }
  });

  function handleOpenSheet({ detail }) {
    showLibrary = false;
    sheetUrl = detail;
    localStorage.setItem("lastSheet", sheetUrl);
  }
</script>

<style>
  .library-wrapper {
    z-index: 100;
    position: absolute;
    top: 0;
    left: 0;
    background: #eee;
    width: 100vw;
    max-width: 100%;
  }

  .message {
    margin: 1rem 0;
    font-size: 1.25rem;
    font-weight: bold;
    text-align: center;
    width: 100%;
  }

  .message-action {
    width: 100%;
    display: flex;
    justify-content: center;
  }
</style>

<main>
  {#if showLibrary}
    <main
      class="library-wrapper shadow-md"
      transition:slide={{ duration: 600, easing: cubicInOut }}>
      <Library
        on:closeLibrary={() => (showLibrary = false)}
        on:opensheet={handleOpenSheet} />
    </main>
  {/if}
  {#if sheetUrl}
    <Viewer bind:sheetUrl on:showLibrary={() => (showLibrary = true)} />
  {:else}
    <div class="message">Open a sheet to get started</div>
    <div class="message-action">
      <button on:click={() => (showLibrary = true)}>Open Library</button>
    </div>
  {/if}
</main>
