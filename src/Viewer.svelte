<script>
import { createEventDispatcher, onMount } from 'svelte';
import opensheetmusicdisplay from 'opensheetmusicdisplay';
import { writable } from 'svelte/store';

let title = 'The Title';
export let sheetUrl;
let showMenu = true;
let compact = true;
let osmd;

const dispatch = createEventDispatcher();

onMount(async () => {
  compact = JSON.parse(localStorage.getItem('compact')) || false;
  osmd = new opensheetmusicdisplay.OpenSheetMusicDisplay('sheet-viewer', {
    drawingParameters: compact ? 'compact' : 'normal',
    drawPartNames: false,
  });
  await osmd.load(sheetUrl);
  osmd.render();
});

function onViewModeChange(e) {
  if (e.target.checked) {
    localStorage.setItem('compact', JSON.stringify(true));
    osmd.setOptions({ drawingParameters: 'compact' });
  } else {
    localStorage.setItem('compact', JSON.stringify(false));
    osmd.setOptions({ drawingParameters: 'normal' });
  }
  osmd.render();
}

</script>


<main>
    {#if showMenu}
      <nav class="menu shadow-md">
        <button class="nav-button" on:click={() => showMenu = false}>
          <i class="material-icons ">close</i>
          <span class="button-text ">
          Close Menu
          </span>
        </button>
        <label for="compact" class="checkbox-label">
          Compact
          <input type="checkbox" name="compact" id="compact" on:change={onViewModeChange} bind:checked={compact}>
        </label>
        <button class="nav-button" on:click={() => dispatch('showLibrary')}>
          <span class="button-text pl-1">
          Show Library&nbsp;
          </span>
          <i class="material-icons">library_music</i>
        </button>
      </nav>
    {:else}
      <button class="subtle-button" title="Show Menu" on:click={() => showMenu = true}>
        <i class="material-icons">menu</i></button>
    {/if}
  <div id="sheet-viewer"></div>
</main>

<style>
  .menu {
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 2rem;
    z-index: 500;
    background: #eee;
  }

  .checkbox-label {
    display: flex;
    align-items: center;
  }

  #sheet-viewer {
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
  }
</style>


