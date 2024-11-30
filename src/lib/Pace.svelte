<script lang="ts">
  let stats = $state({
    duration: null as number | null,
    distance: null as number | null,
    pace: null as string | null,
  });

  function parsePace(pace: string) {
    if (!pace?.includes(":")) return parseFloat(pace);
    const [min, sec] = pace.split(":").map(Number);
    return min + sec / 60;
  }

  function formatPace(pace: number) {
    const min = Math.floor(pace);
    const sec = Math.round((pace - min) * 60);
    return `${min}:${sec.toString().padStart(2, "0")}`;
  }

  let completed_stats = $derived.by(() => {
    if (!stats.duration && !stats.distance && !stats.pace) return null;

    if (stats.duration && stats.distance) {
      return { pace: formatPace(stats.duration / stats.distance) };
    }
    if (stats.duration && stats.pace) {
      return { distance: stats.duration / parsePace(stats.pace) };
    }
    if (stats.distance && stats.pace) {
      return { duration: stats.distance * parsePace(stats.pace) };
    }
  });
</script>

<main style="display: flex; flex-direction: row">
  <div style="padding: 10px">
    <label for="duration"><b>Duration</b></label>
    {#if completed_stats && completed_stats.duration}
      <input
        type="number"
        id="duration"
        value={completed_stats.duration.toFixed(2)}
        style="border: 3px solid green;"
        readonly
      />
    {:else}
      <input
        type="number"
        id="duration"
        bind:value={stats.duration}
        placeholder="Duration in minutes"
      />
    {/if}
  </div>

  <div style="padding: 10px">
    <label for="pace"><b>Pace</b></label>
    {#if completed_stats && completed_stats.pace}
      <input
        type="string"
        id="pace"
        value={completed_stats.pace}
        readonly
        style="border: 3px solid green;"
      />
    {:else}
      <input
        type="string"
        id="pace"
        bind:value={stats.pace}
        placeholder="Pace in mm:ss per km"
      />
    {/if}
  </div>

  <div style="padding: 10px">
    <label for="distance"><b>Distance</b></label>
    {#if completed_stats && completed_stats.distance}
      <input
        type="number"
        id="distance"
        value={completed_stats.distance.toFixed(2)}
        style="border: 3px solid green;"
        readonly
      />
    {:else}
      <input
        type="number"
        id="distance"
        bind:value={stats.distance}
        placeholder="Distance in km"
      />
    {/if}
  </div>
</main>
