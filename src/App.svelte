<script>
import Viewer from './Viewer.svelte';
import Library from './Library.svelte';
import { slide } from 'svelte/transition';
import { cubicInOut } from 'svelte/easing';

let sheetUrl = 'https://minio.rickhenry.dev/musicxmls/andante-festivo%20-%2001_Violin%20I%20-%2001%20Flow%201.mxl?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=3rVLYahQf1mIsm2wfRo9fuoYp%2F20200410%2F%2Fs3%2Faws4_request&X-Amz-Date=20200410T020015Z&X-Amz-Expires=432000&X-Amz-SignedHeaders=host&X-Amz-Signature=732fa5acf3deb05fa191dfd5db2441e87b47204e51265d1256ca3c12b673f18c';
let showLibrary = false;


</script>

<main>
    {#if showLibrary}
      <main class="library-wrapper shadow-md" transition:slide={{duration: 600, easing: cubicInOut}}>
        <Library on:closeLibrary={() => showLibrary = false} />
      </main>
    {/if}
  <Viewer bind:sheetUrl on:showLibrary={() => showLibrary = true} />
</main>


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
