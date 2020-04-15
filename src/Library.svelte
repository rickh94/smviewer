<script>
  import { createEventDispatcher, onMount } from "svelte";
  import { slide } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";
  import LibraryItem from "./LibraryItem.svelte";
  import EditDialog from "./EditDialog.svelte";

  const dispatch = createEventDispatcher();
  let library = {
    sheets: []
  };
  let addMenuOpen = false;
  let showAddDialog = false;
  let addMenuEl;
  let sheetForDeletion = null;
  let sheetForEditing = null;

  onMount(() => {
    // console.log(new URL(window.location).searchParams.get("add"));
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

  function deleteSheet() {
    const nextSheets = library.sheets.filter(
      sheet => sheet.id !== sheetForDeletion.id
    );
    library.sheets = nextSheets;
    localStorage.setItem("library", JSON.stringify(library));
    sheetForDeletion = null;
  }

  function updateSheet({detail: nextSheet}) {
    const sheetIdx = library.sheets.findIndex(currentSheet => currentSheet.id === nextSheet.id);
    library.sheets[sheetIdx] = nextSheet;
    localStorage.setItem('library', JSON.stringify(library));
    sheetForEditing = null;
  }

</script>

<style>
  h2 {
    text-align: center;
  }

  h4 {
    margin: 0 0 0.5rem 0;
  }

  .dialog-wrapper {
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

  .dialog-text {
    margin-bottom: 1rem;
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
  <div class="dialog-wrapper">
    <EditDialog
      on:savesheet={handleAddSheet}
      on:cancel={() => (showAddDialog = false)} />
  </div>
{:else if sheetForDeletion !== null}
  <div class="dialog-wrapper">
    <h4>Confirm Deletion</h4>
    <p class="dialog-text">
      Are you sure you want to delete {sheetForDeletion.title}?
    </p>
    <div class="dialog-buttons">
      <button on:click={deleteSheet}>Delete</button>
      <button on:click={() => (sheetForDeletion = null)}>Cancel</button>
    </div>
  </div>
{:else if sheetForEditing !== null}
  <div class="dialog-wrapper">
    <EditDialog
      {...sheetForEditing}
      on:savesheet={updateSheet}
      on:cancel={() => (sheetForEditing = null)} />
  </div>
{:else}
  <div class="library">
    {#each library.sheets as sheet (sheet.id)}
      <div class="grid-item shadow">
        <LibraryItem
          {sheet}
          on:opensheet
          on:deletesheet={({ detail: sheet }) => (sheetForDeletion = sheet)}
          on:editsheet={({ detail: sheet }) => (sheetForEditing = sheet)} />
      </div>
    {:else}
      <div class="grid-item">
        Nothing in Library! Click above to Add something.
      </div>
    {/each}
  </div>
{/if}
