<script lang="ts">
  import { mdiMagnify } from '@mdi/js';
  import { color as d3color, hsl as d3hsl, type HSLColor, type RGBColor } from 'd3-color';
  import { AppBar, CopyButton, TextField, ToggleGroup, ToggleOption, cls } from 'svelte-ux';
  import { Arc, Chart, Group, radiansToDegrees, Svg } from 'layerchart';

  import twColors from 'tailwindcss/colors';

  const deprecatedColors = ['lightBlue', 'warmGray', 'trueGray', 'coolGray', 'blueGray'];
  const colors = Object.entries(twColors).filter(
    ([name, values]) => typeof values === 'object' && !deprecatedColors.includes(name)
  );

  let colorStr = twColors['blue']['500'];
  let selectedTab: 'tailwind' | 'wheel' = 'tailwind';

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

  // color wheel
  let layerCount = 6;
  let divisions = 12;

  function wheelSegmentColor(
    startAngle: number, // in radians
    layer: number,
    type: 'saturation' | 'lightness' | 'alpha' = 'saturation'
  ) {
    const angle = Math.round(radiansToDegrees(startAngle));
    switch (type) {
      case 'saturation':
        return `hsla(${angle}, ${Math.round((layer / layerCount) * 100)}%, 50%, 1)`;
      case 'lightness':
        return `hsla(${angle}, 100%, ${100 - 10 * layer}%, 1)`;
      case 'alpha':
        return `hsla(${angle}, 100%, 50%, ${layer / layerCount})`;
    }
  }
</script>

<AppBar title="Color" />

<main class="p-4">
  <section class="grid gap-3">
    <TextField label="Parse color" icon={mdiMagnify} bind:value={colorStr}>
      <svelte:fragment slot="append">
        <div style:background={rgb} class="w-6 h-6 rounded-lg" />
      </svelte:fragment>
    </TextField>

    <div class="grid grid-cols-3 gap-3">
      <TextField label="HEX" value={hex}>
        <svelte:fragment slot="append">
          <CopyButton value={hex} size="sm" />
        </svelte:fragment>
      </TextField>

      <TextField label="RGB" value={rgb}>
        <svelte:fragment slot="append">
          <CopyButton value={rgb} size="sm" />
        </svelte:fragment>
      </TextField>

      <TextField label="HSL" value={hsl}>
        <svelte:fragment slot="append">
          <CopyButton value={hsl} size="sm" />
        </svelte:fragment>
      </TextField>
    </div>

    <div class="bg-white rounded border border-gray-300">
      <ToggleGroup
        bind:value={selectedTab}
        variant="underline"
        classes={{ options: 'justify-start h-10' }}
      >
        <ToggleOption value="tailwind">Tailwind</ToggleOption>
        <ToggleOption value="wheel">Color wheel</ToggleOption>
      </ToggleGroup>

      {#if selectedTab === 'tailwind'}
        <div>
          <div class="grid grid-cols-12 items-center gap-[1px]">
            {#each colors as [name, values]}
              <div class="text-sm text-right mr-2">{name}</div>
              {#each Object.entries(values) as [shade, color]}
                {@const colorHex = twColors[name][shade]}
                <button
                  style:background={color}
                  class={cls(
                    'h-8 rounded-sm text-center',
                    colorHex === hex && 'border-2 border-white/50'
                  )}
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
            <a
              class="text-accent-600"
              href="https://tailwindcss.com/docs/customizing-colors"
              target="_blank"
            >
              Tailwind docs
            </a>
            or
            <a href="https://www.radix-ui.com/colors" class="text-accent-600" target="_blank"
              >Radix UI</a
            >
            colors
          </div>
        </div>
      {:else}
        <div class="h-[600px] p-4 border rounded">
          <Chart>
            <Svg>
              <Group center class="group">
                {#each { length: layerCount } as _, layerIndex}
                  {@const layer = layerIndex + 1}
                  {#each { length: divisions } as _, segmentIndex}
                    {@const segmentAngle = (2 * Math.PI) / divisions}
                    {@const startAngle = segmentIndex * segmentAngle}
                    {@const endAngle = (segmentIndex + 1) * segmentAngle}
                    {@const color = wheelSegmentColor(startAngle, layer)}
                    <Arc
                      {startAngle}
                      {endAngle}
                      outerRadius={layer / layerCount}
                      innerRadius={-42}
                      cornerRadius={4}
                      padAngle={0.02}
                      fill={color}
                      class="hover:!opacity-100 group-hover:opacity-50 transition duration-300"
                      on:click={() => (colorStr = color)}
                    />
                  {/each}
                {/each}
              </Group>
            </Svg>
          </Chart>
        </div>
      {/if}
    </div>
  </section>
</main>
