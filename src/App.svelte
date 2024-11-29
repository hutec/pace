<script lang="ts">
  import Pace from "./lib/Pace.svelte";

  let paces = $state([Pace]);

  function addPace() {
    paces = [...paces, Pace];
  }
</script>

<main>
  {#each paces as pace}
    <svelte:component this={pace} />
  {/each}

  <button onclick={addPace}>Add Pace</button>
</main>

<style>
</style>
