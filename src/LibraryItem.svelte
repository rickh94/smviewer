<script>
  import { createEventDispatcher } from "svelte";
  import { slide } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";

  let threeDotsOpen = false;
  let menuElement;

  export let sheet = {
    title: "",
    id: null,
    musicURL: "",
    previewURL: null
  };

  const dispatch = createEventDispatcher();

  function onOpenSheet() {
    dispatch("opensheet", sheet.musicURL);
  }

  function openMenu() {
    setTimeout(() => {
      document.addEventListener("keydown", closeMenuOnEscape);
      document.addEventListener("click", closeOnOtherClick);
    }, 50);
    threeDotsOpen = true;
  }

  function closeMenuOnEscape(e) {
    if (e.key === "Escape") {
      closeMenu();
    }
  }

  function closeMenu() {
    threeDotsOpen = false;
    document.removeEventListener("keydown", closeMenuOnEscape);
    document.removeEventListener("click", closeOnOtherClick);
  }

  function closeOnOtherClick(e) {
    if (!menuElement.contains(e.target)) {
      closeMenu();
    }
  }

  function onDeleteSheet() {
    closeMenu();
    dispatch("deletesheet", sheet);
  }

  function onEditSheet() {
    closeMenu();
    dispatch("editsheet", sheet);
  }

</script>

<style>
  .img-wrapper {
    height: 11rem;
    overflow: hidden;
    cursor: pointer;
    border-bottom: 1px solid black;
  }

  img {
    width: 100%;
    overflow: hidden;
  }

  .title,
  .composer {
    margin: 0.25rem;
    margin-left: 0.375rem;
  }

  .title {
    font-weight: bold;
    cursor: pointer;
  }

  .composer {
    color: #666;
  }

  .three-dots {
    /* margin-left: auto; */
    display: block;
    border: none;
    padding: 0;
    padding-left: 0.25rem;
    padding-right: 0.25rem;
    background: white;
    cursor: pointer;
    position: absolute;
    /* padding-bottom: 0.25rem; */
    bottom: 0.25rem;
    right: 0;
    z-index: 100;
  }

  .dropdown {
    position: absolute;
    bottom: 0rem;
    right: 0;
  }

  .dropdown-wrapper {
    margin-top: auto;
  }

  .bottom {
    height: 4rem;
    position: relative;
  }

  .dropdown-content {
    position: absolute;
    background-color: #f9f9f9;
    width: 5.5rem;
    z-index: 150;
    bottom: 0;
    right: 0;
  }

  .dropdown-content a,
  .dropdown-content button {
    color: black;
    padding: 0.25rem 0.5rem;
    text-decoration: none;
    display: block;
    font-size: 1rem;
    border: none;
    background: transparent;
    cursor: pointer;
    height: 100%;
    width: auto;
    z-index: 300;
    overflow: visible;
    width: 100%;
    text-align: right;
  }

  .dropdown-content button i {
    display: inline;
  }

  .dropdown-content a:hover,
  .dropdown-content button:hover {
    background-color: #f1f1f1;
  }

  .icon-sm {
    font-size: 1rem;
  }
</style>

<div class="img-wrapper" on:click={onOpenSheet}>
  <img
    src={sheet.previewURL}
    alt="Could not find preview"
    title="Open {sheet.title}" />
</div>
<div class="bottom">
  <div class="title" on:click={onOpenSheet} title="Open {sheet.title}">
    {sheet.title}
  </div>
  {#if sheet.composer}
    <div class=" composer">{sheet.composer}</div>
  {/if}
  <div class="dropdown-wrapper">
    <div class="dropdown">
      <button class="three-dots" title="Options" on:click={openMenu}>
        <i class="material-icons">more_horiz</i>
      </button>
      {#if threeDotsOpen}
        <div
          class="dropdown-content shadow-md"
          bind:this={menuElement}
          transition:slide={{ duration: 200, easing: cubicInOut }}>
          <button on:click={onDeleteSheet}>
              <i class="material-icons icon-sm">delete</i>
              Delete
          </button>
          <button on:click={onEditSheet}>
            <i class="material-icons icon-sm">edit</i>
            Edit
          </button>
        </div>
      {/if}
    </div>
  </div>
</div>
