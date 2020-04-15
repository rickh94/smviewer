<script>
  import { createEventDispatcher } from "svelte";
  export let id = null;
  export let title = "";
  export let composer = "";
  export let musicURL = "";
  export let previewURL = "";
  let error = "";

  $: titleError = error.includes("Title");
  $: urlError = error.includes("URL");

  const dispatch = createEventDispatcher();

  function saveSheet() {
    if (!title) {
      error = "Title is required";
      return;
    }
    if (!musicURL) {
      error = "Music URL is required";
      return;
    }
    if (!musicURL.includes("http")) {
      error = "Please enter a full URL";
      return;
    }
    if (id === null) {
      id = Math.random()
        .toString(36)
        .substr(2, 9);
    }

    dispatch("savesheet", { id, title, composer, musicURL, previewURL });
  }
</script>

<style>
  h4 {
    margin: 0 0 0.5rem 0;
  }

  .error {
    color: #fe0505;
  }

  .error-message {
    margin-bottom: 0.5rem;
  }

  label {
    display: block;
    padding-bottom: 0.25rem;
  }

  input {
    border: 1px solid #333;
    background: #fafafa;
    margin-bottom: 0.5rem;
    padding: 0.25rem 0.5rem;
    width: 100%;
    max-width: 20rem;
  }

  input:focus {
    background: white;
  }

  input.error {
    border-color: #fe0505;
  }
</style>

{#if id === null}
  <h4>Add a New Piece</h4>
{:else}
  <h4>Edit {title}</h4>
{/if}
{#if error}
  <div class="error error-message">{error}</div>
{/if}
<label for="title" class:error={titleError}>Title</label>
<input
  type="text"
  id="title"
  class="focus:shadow"
  bind:value={title}
  class:error={titleError} />
<label for="composer">Composer</label>
<input type="text" id="composer" class="focus:shadow" bind:value={composer} />
<label for="musicURL" class:error={urlError}>URL to Music File</label>
<input
  type="text"
  id="musicURL"
  class="focus:shadow"
  bind:value={musicURL}
  class:error={urlError} />
<label for="previewURL">URL to Preview Image</label>
<input
  type="text"
  id="previewURL"
  class="focus:shadow"
  bind:value={previewURL} />
<div class="dialog-buttons">
  <button type="submit" class="hover:shadow" on:click={saveSheet}>Save</button>
  <button class="hover:shadow" on:click={() => dispatch('cancel')}>
    Cancel
  </button>
</div>
