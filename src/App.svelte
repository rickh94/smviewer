<script>
  import Viewer from "./Viewer.svelte";
  import Library from "./Library.svelte";
  import { slide } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";

  let sheetUrl =
    "https://f000.backblazeb2.com/file/musicxml-files/french-folk-song-v2.mxl";
  // let showLibrary = false;
  let showLibrary = true;

  function handleOpenSheet({detail}) {
    showLibrary = false;
    sheetUrl = detail;
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
  <Viewer bind:sheetUrl on:showLibrary={() => (showLibrary = true)} />
</main>
