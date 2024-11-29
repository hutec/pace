<script lang="ts">
  let stats = $state({
    duration: null,
    distance: null,
    pace: null,
  });

  function parsePace(pace: string): number | null {
    if (!pace) return null;
    if (pace.includes(":")) {
      const [minutes, seconds] = pace.split(":").map(Number);
      return minutes + seconds / 60;
    } else {
      return parseFloat(pace);
    }
  }

  function formatPace(pace: number): string {
    const minutes = Math.floor(pace);
    const seconds = Math.round((pace - minutes) * 60);
    return `${minutes}:${seconds.toString().padStart(2, "0")}`;
  }

  let completed_stats = $derived.by(() => {
    // Derive the missing stat from the other two
    let defaults = {
      duration: null,
      distance: null,
      pace: null,
    };

    if (stats.duration && stats.distance) {
      return {
        ...defaults,
        pace: formatPace(stats.duration / stats.distance),
      };
    }

    if (stats.duration && stats.pace) {
      return {
        ...defaults,
        distance: stats.duration / parsePace(stats.pace),
      };
    }

    if (stats.distance && stats.pace) {
      return {
        ...defaults,
        duration: stats.distance * parsePace(stats.pace),
      };
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
