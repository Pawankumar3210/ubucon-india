<script>
  let { volunteers = [] } = $props();

  const teams = $derived([
    'All',
    ...Array.from(new Set(volunteers.flatMap((v) => v.team))).sort(),
  ]);

  let active = $state('All');

  const shown = $derived(
    active === 'All' ? volunteers : volunteers.filter((v) => v.team.includes(active)),
  );

  const teamChip = {
    Core: 'p-chip--information',
    Web: 'p-chip--positive',
    Design: 'p-chip--caution',
    Media: 'p-chip--negative',
  };

  const avatar = (handle) => `https://github.com/${handle}.png?size=400`;
</script>

{#if teams.length > 1}
  <div role="group" aria-label="Filter volunteers by team">
    {#each teams as team}
      <button
        type="button"
        class={teamChip[team] || 'p-chip'}
        aria-pressed={active === team}
        onclick={() => (active = team)}
      >
        {#if active === team}<i class="p-icon--success-grey"></i>{/if}
        <span class="p-chip__value">{team}</span>
      </button>
    {/each}
  </div>
{/if}

<div class="grid-row--25-25-25-25">
  {#each shown as v (v.github)}
    <div class="grid-col">
      <div class="p-card u-align--center">
        <img src={avatar(v.github)} alt={v.name} width="96" height="96" loading="lazy" />
        <h3 class="p-card__title">{v.name}</h3>
        {#each v.team as team}
          <span class="{teamChip[team] || 'p-chip'} is-read-only">
            <span class="p-chip__value">{team}</span>
          </span>
        {/each}
        <a class="p-button--link is-small" href="https://github.com/{v.github}" target="_blank" rel="noopener noreferrer">
          @{v.github}
        </a>
      </div>
    </div>
  {/each}
</div>

<style>
  .grid-row--25-25-25-25 {
    padding: 0;
    max-width: none;
    grid-auto-rows: 1fr; /* every row as tall as the tallest card */
    row-gap: 2rem; /* Vanilla's grid gutter is horizontal only */
  }
  /* Fill the cell, so a wrapped name doesn't make one card taller. */
  .grid-col > .p-card { height: 100%; margin-bottom: 0; }

  /* Round the square GitHub avatar; Vanilla has no circular-image utility. */
  .p-card img {
    width: 96px;
    height: 96px;
    border-radius: 50%;
    object-fit: cover;
  }
  /* Keeps the handle on its own line, under the team chips. */
  .p-card .p-button--link { display: block; }
</style>
