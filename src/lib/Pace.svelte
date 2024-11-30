<script lang="ts">
  interface PaceStats {
    duration: number | null;
    distance: number | null;
    pace: string | null;
  }

  interface CompletedStats {
    pace?: string;
    distance?: number;
    duration?: number;
  }

  let stats = $state<PaceStats>({
    duration: null,
    distance: null,
    pace: null,
  });

  function parsePace(pace: string): number {
    if (!pace?.includes(":")) {
      const num = parseFloat(pace);
      return isNaN(num) ? 0 : num;
    }
    const [min, sec] = pace.split(":").map(Number);
    return min + sec / 60;
  }

  function formatPace(pace: number): string {
    if (pace <= 0) return "0:00";
    const min = Math.floor(pace);
    const sec = Math.round((pace - min) * 60);
    return `${min}:${sec.toString().padStart(2, "0")}`;
  }

  function clearForm(): void {
    stats = {
      duration: null,
      distance: null,
      pace: null,
    };
  }

  let completed_stats = $derived.by((): CompletedStats | null => {
    if (!stats.duration && !stats.distance && !stats.pace) return null;

    try {
      if (stats.duration && stats.distance && stats.distance > 0) {
        return { pace: formatPace(stats.duration / stats.distance) };
      }
      if (stats.duration && stats.pace) {
        const pace = parsePace(stats.pace);
        if (pace > 0) return { distance: stats.duration / pace };
      }
      if (stats.distance && stats.pace) {
        return { duration: stats.distance * parsePace(stats.pace) };
      }
    } catch (error) {
      console.error("Calculation error:", error);
    }
    return null;
  });
</script>

<main class="container">
  <div class="input-group">
    <label for="duration">Duration</label>
    {#if completed_stats?.duration}
      <div class="input-with-unit">
        <input
          type="number"
          id="duration"
          value={completed_stats.duration.toFixed(2)}
          class="completed"
          readonly
        />
      </div>
    {:else}
      <div class="input-with-unit">
        <input
          type="number"
          id="duration"
          bind:value={stats.duration}
          placeholder="minutes"
        />
      </div>
    {/if}
  </div>

  <div class="input-group">
    <label for="distance">Distance</label>
    {#if completed_stats?.distance}
      <div class="input-with-unit">
        <input
          type="number"
          id="distance"
          value={completed_stats.distance.toFixed(2)}
          class="completed"
          readonly
        />
      </div>
    {:else}
      <div class="input-with-unit">
        <input
          type="number"
          id="distance"
          bind:value={stats.distance}
          placeholder="km"
        />
      </div>
    {/if}
  </div>

  <div class="input-group">
    <label for="pace">Pace</label>
    {#if completed_stats?.pace}
      <div class="input-with-unit">
        <input
          type="text"
          id="pace"
          value={completed_stats.pace}
          class="completed"
          readonly
        />
      </div>
    {:else}
      <div class="input-with-unit">
        <input
          type="text"
          id="pace"
          bind:value={stats.pace}
          placeholder="mm:ss"
        />
      </div>
    {/if}
  </div>

  <button class="clear-btn" on:click={clearForm}>Clear All</button>
</main>

<style>
  .container {
    display: flex;
    flex-direction: row;
    gap: 1rem;
    padding: 1rem;
  }

  .input-group {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .input-with-unit {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  input {
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 150px;
  }

  input.completed {
    border: 2px solid #4caf50;
    background-color: #f0fff0;
  }

  .clear-btn {
    padding: 0.25rem 0.75rem;
    background-color: #f44336;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
  }

  .clear-btn:hover {
    background-color: #d32f2f;
  }
</style>
