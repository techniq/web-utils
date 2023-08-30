<script lang="ts">
  import { mdiMagnify } from '@mdi/js';
  import { color as d3color, hsl as d3hsl, type HSLColor, type RGBColor } from 'd3-color';
  import { AppBar, CopyButton, TextField, cls } from 'svelte-ux';

  import twColors from 'tailwindcss/colors';

  const deprecatedColors = ['lightBlue', 'warmGray', 'trueGray', 'coolGray', 'blueGray'];
  const colors = Object.entries(twColors).filter(
    ([name, values]) => typeof values === 'object' && !deprecatedColors.includes(name)
  );

  let colorStr = twColors['blue']['500'];

  // Computed from selected
  $: parsedColor = d3color(colorStr);
  $: hex = parsedColor?.formatHex() ?? '';
  $: rgb = parsedColor?.formatRgb() ?? '';
  $: hsl = formatHsl(parsedColor);

  function round(value, decimals) {
    return Number(value.toFixed(decimals));
  }

  function formatHsl(color: RGBColor | HSLColor | null, decimals = 2) {
    if (color == null) {
      return '';
    }

    const hslColor = d3hsl(color);

    // Format as hsl() with adjustable decimal precision (unlike d3-color's .formatHsl())
    const h = round(hslColor.h, decimals);
    const s = round(hslColor.s * 100, decimals);
    const l = round(hslColor.l * 100, decimals);
    return `hsl(${h}, ${s}%, ${l}%)`;
  }
</script>

<AppBar title="Color" />

<main class="p-4">
  <section class="grid gap-3">
    <TextField label="Parse color" icon={mdiMagnify} bind:value={colorStr} />

    <div class="grid grid-cols-4 gap-3">
      <TextField label="hex" value={hex}>
        <svelte:fragment slot="append">
          <CopyButton value={hex} size="sm" />
        </svelte:fragment>
      </TextField>

      <TextField label="rgb" value={rgb}>
        <svelte:fragment slot="append">
          <CopyButton value={rgb} size="sm" />
        </svelte:fragment>
      </TextField>

      <TextField label="hsl" value={hsl}>
        <svelte:fragment slot="append">
          <CopyButton value={hsl} size="sm" />
        </svelte:fragment>
      </TextField>
    </div>

    <a
      class="text-lg text-accent-600"
      href="https://tailwindcss.com/docs/customizing-colors"
      target="_blank"
    >
      Tailwind colors
    </a>

    <div class="grid grid-cols-12 items-center gap-[1px]">
      {#each colors as [name, values]}
        <div class="text-sm text-right mr-2">{name}</div>
        {#each Object.entries(values) as [shade, color]}
          {@const colorHex = twColors[name][shade]}
          <button
            style:background={color}
            class={cls('h-8 rounded-sm text-center', colorHex === hex && 'border-2')}
            on:click={() => (colorStr = colorHex)}
          >
            <span class={cls('text-xs', shade > 500 ? 'text-white/30' : 'text-black/30')}>
              {shade}
            </span>
          </button>
        {/each}
      {/each}
    </div>

    <div>
      See also:
      <a href="https://www.radix-ui.com/colors" class="text-accent-600" target="_blank">Radix UI</a>
    </div>
  </section>
</main>
