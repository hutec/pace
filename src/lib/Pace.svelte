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

  let fields = [
    {
      id: "duration",
      label: "Duration",
      unit: "minutes",
      format: (v) => v.toFixed(2),
    },
    { id: "pace", label: "Pace", unit: "mm:ss per km", format: (v) => v },
    {
      id: "distance",
      label: "Distance",
      unit: "km",
      format: (v) => v.toFixed(2),
    },
  ];
</script>

<main style="display: flex; flex-direction: row">
  {#each fields as field}
    <div style="padding: 10px">
      <label for={field.id}><b>{field.label}</b></label>
      {#if completed_stats && completed_stats[field.id]}
        <input
          type={field.id === "pace" ? "text" : "number"}
          id={field.id}
          value={field.format(completed_stats[field.id])}
          style="border: 3px solid green;"
          readonly
        />
      {:else}
        <input
          type={field.id === "pace" ? "text" : "number"}
          id={field.id}
          bind:value={stats[field.id]}
          placeholder="{field.label} in {field.unit}"
        />
      {/if}
    </div>
  {/each}
</main>
