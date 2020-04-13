<script>
  import { createEventDispatcher, onMount } from "svelte";
  import { slide } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";
  import LibraryItem from "./LibraryItem.svelte";
  import AddDialog from "./AddDialog.svelte";

  const dispatch = createEventDispatcher();
  let library = {
    sheets: []
  };
  let addMenuOpen = false;
  let showAddDialog = false;
  let addMenuEl;
  let sheetForDeletion = null;

  onMount(() => {
    library = JSON.parse(localStorage.getItem("library")) || {
      sheets: []
    };
  });

  function toggleAddMenu() {
    if (addMenuOpen) {
      closeAddMenu();
    } else {
      addMenuOpen = true;
      setTimeout(() => {
        document.addEventListener("keydown", closeAddMenuOnEscape);
        document.addEventListener("click", closeOnOtherClick);
      }, 50);
    }
  }

  function closeAddMenuOnEscape(e) {
    if (e.key === "Escape") {
      closeAddMenu();
    }
  }

  function closeOnOtherClick(e) {
    if (!addMenuEl.contains(e.target)) {
      closeAddMenu();
    }
  }

  function closeAddMenu() {
    addMenuOpen = false;
    document.removeEventListener("keydown", closeAddMenuOnEscape);
    document.removeEventListener("click", closeOnOtherClick);
  }

  function openAddDialog() {
    toggleAddMenu();
    showAddDialog = true;
  }

  function handleAddSheet({ detail }) {
    library.sheets.push(detail);
    localStorage.setItem("library", JSON.stringify(library));
    console.log(library.sheets);
    showAddDialog = false;
  }
  
  function handleDeleteSheet({detail: sheet}) {
    // alert(`delete called for ${sheet.id} ${sheet.title}`);
    sheetForDeletion = sheet;
  }

  function handleEditSheet({detail: sheet}) {
    alert(`edit called for ${sheet.id} ${sheet.title}`);
  }

  function deleteSheet() {
    const nextSheets = library.sheets.filter(sheet => sheet.id !== sheetForDeletion.id);
    library.sheets = nextSheets;
    localStorage.setItem("library", JSON.stringify(library));
    sheetForDeletion = null;
  }
</script>

<style>
  h2 {
    text-align: center;
  }

  .add-wrapper {
    margin: 0 2rem 2rem 1.5rem;
  }

  .menu {
    display: flex;
    justify-content: space-between;
    margin: 0 1rem;
    align-items: center;
  }

  .library {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(10rem, 1fr));
    margin: 0 1rem 2rem 1rem;
    grid-gap: 1rem;
  }

  .grid-item {
    height: 15rem;
    /*border: 1px solid black;*/
    background: white;
    color: #333;
  }

  .dropdown {
    position: relative;
  }

  .dropdown-content {
    position: absolute;
    background-color: #f9f9f9;
    min-width: 160px;
    z-index: 150;
  }

  .dropdown-content a,
  .dropdown-content button {
    color: black;
    padding: 0.75rem 1rem;
    text-decoration: none;
    display: block;
    font-size: 1rem;
    border: none;
    background: transparent;
    cursor: pointer;
    height: 100%;
    width: auto;
    text-align: left;
  }

  .dropdown-content a:hover,
  .dropdown-content button:hover {
    background-color: #f1f1f1;
  }

  .add-menu-button {
    min-width: 160px;
    outline: none;
  }
</style>

<nav class="menu">
  <div class="dropdown">
    <button
      class="nav-button add-menu-button"
      on:click={toggleAddMenu}
      class:active={addMenuOpen}>
      <i class="material-icons">add</i>
      <span class="button-text">Add to Library</span>
    </button>
    {#if addMenuOpen}
      <div
        bind:this={addMenuEl}
        class="dropdown-content shadow-md"
        transition:slide={{ duration: 200, easing: cubicInOut }}>
        <a href="#">Add from Trenton Music Makers</a>
        <button on:click={openAddDialog}>Add from Public URL</button>
      </div>
    {/if}
  </div>
  <h2>My Music</h2>
  <button
    class="nav-button right-button"
    on:click={() => dispatch('closeLibrary')}>
    <span class="button-text">Close Library</span>
    <i class="material-icons">close</i>
  </button>
</nav>
{#if showAddDialog}
  <div class="add-wrapper">
    <AddDialog
      on:addsheet={handleAddSheet}
      on:cancel={() => (showAddDialog = false)} />
  </div>
  {:else if sheetForDeletion}
  <div class="delete-wrapper">
  <h6>Are you sure you want to delete {sheetForDeletion.title}?</h6>
  <button on:click={deleteSheet}>Delete</button>
  <button on:click={() => sheetForDeletion = null}>Cancel</button>
  </div>
{:else}
  <div class="library">
    {#each library.sheets as sheet (sheet.id)}
      <div class="grid-item shadow">
        <LibraryItem {sheet} on:opensheet on:deletesheet={handleDeleteSheet} on:editsheet={handleEditSheet} />
      </div>
    {:else}
      <div class="grid-item">
        Nothing in Library! Click above to Add something.
      </div>
    {/each}
  </div>
{/if}
