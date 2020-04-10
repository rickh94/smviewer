<script>
import { createEventDispatcher } from 'svelte';

const dispatch = createEventDispatcher();

</script>
<nav class="menu">
  <button class="nav-button left-button">
    <i class="material-icons">add</i>
    <span class="button-text">
    Add to Library
    </span>
  </button>
  <h2>My Music</h2>
  <button class="nav-button right-button" on:click={() => dispatch('closeLibrary')}>
    <span class="button-text">
    Close Library
    </span>
    <i class="material-icons">close</i>
  </button>
</nav>
<div class="div-with-height"></div>
<div class="div-with-height"></div>
<div class="div-with-height"></div>
<div class="div-with-height"></div>
<div class="div-with-height"></div>
<div class="div-with-height"></div>
<div class="div-with-height"></div>

<style>
  h2 {
    text-align: center;
  }

  .menu {
    display: flex;
    justify-content: space-between;
    margin: 0 1rem;
    align-items: center;
  }


  .div-with-height {
    height: 5rem;
  }
</style>

