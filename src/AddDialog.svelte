<script>
  import { createEventDispatcher } from "svelte";
  let title = "";
  let composer = "";
  let musicURL = "";
  let previewURL = "";
  let error = "";

  $: titleError = error.includes("Title");
  $: urlError = error.includes("URL");

  const dispatch = createEventDispatcher();

  function addSheet() {
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
    const id = Math.random()
      .toString(36)
      .substr(2, 9);

    dispatch("addsheet", { id, title, composer, musicURL, previewURL });
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

  .buttons {
    margin-top: 0.5rem;
    width: 100%;
    max-width: 20rem;
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-gap: 0.5rem;
  }

  button {
    border: 1px solid #333;
    padding: 0.25rem 0.5rem;
    background: #fff;
    cursor: pointer;
  }

  button:hover {
    color: white;
    background: #333;
  }
</style>

<h4>Add a New Piece</h4>
{#if error}
  <div class="error error-message">{error}</div>
{/if}
<label for="title" class:error={titleError}>Title</label>
<input type="text" id="title" class="focus:shadow" bind:value={title} class:error={titleError} />
<label for="composer">Composer</label>
<input type="text" id="composer" class="focus:shadow" bind:value={composer} />
<label for="musicURL" class:error={urlError}>URL to Music File</label>
<input type="text" id="musicURL" class="focus:shadow" bind:value={musicURL} class:error={urlError} />
<label for="previewURL">URL to Preview Image</label>
<input
  type="text"
  id="previewURL"
  class="focus:shadow"
  bind:value={previewURL} />
<div class="buttons">
  <button type="submit" class="hover:shadow" on:click={addSheet}>Save</button>
  <button class="hover:shadow" on:click={() => dispatch('cancel')}>
    Cancel
  </button>
</div>
