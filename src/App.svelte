<script lang="ts">
  import Pace from "./lib/Pace.svelte";

  let paces = [1];

  function addPace() {
    paces = [...paces, paces.length + 1];
  }
</script>

<main>
  {#each paces as pace}
    <Pace />
  {/each}

  <button on:click={addPace}>Add Pace</button>
</main>

<style>
</style>
