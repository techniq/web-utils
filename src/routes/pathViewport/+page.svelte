<script lang="ts">
  import svgpath from 'svgpath';
  import { AppBar, CopyButton, Field, TextField, autoHeight } from 'svelte-ux';

  let inputSize = [512, 512];
  let outputSize = [24, 24];
  let precision = 2;

  $: scaleRatio = Math.max(...outputSize) / Math.max(...inputSize);

  let input = '';
  let output = '';
  $: try {
    output = svgpath(input).scale(scaleRatio).round(precision);
  } catch {
    output = '';
  }
</script>

<AppBar title="Path Viewport" />

<main class="p-2">
  <div class="grid gap-4">
    <section class="grid grid-cols-2 gap-2">
      <h2 class="text-accent-900 text-lg font-semibold">Input</h2>

      <div class="flex gap-1 items-center">
        <span class="text-gray-500 text-sm font-medium">Viewport</span>
        <TextField bind:value={inputSize[0]} type="integer" dense class="w-20" />
        x
        <TextField bind:value={inputSize[1]} type="integer" dense class="w-20" />
      </div>

      <TextField bind:value={input} multiline actions={(node) => [autoHeight(node)]} />

      <div>
        <svg
          viewBox="0 0 {inputSize[0]} {inputSize[1]}"
          class="bg-white border border-gray-300 rounded"
        >
          <path d={input} />
        </svg>
      </div>
    </section>

    <section class="grid grid-cols-2 gap-2">
      <h2 class="text-accent-900 text-lg font-semibold">Output</h2>

      <div class="flex gap-1 items-center">
        <span class="text-gray-500 text-sm font-medium">Viewport</span>
        <TextField bind:value={outputSize[0]} type="integer" dense class="w-20" />
        x
        <TextField bind:value={outputSize[1]} type="integer" dense class="w-20" />
      </div>

      <div>
        <Field class="text-sm relative">
          <CopyButton
            value={output}
            color="accent"
            size="sm"
            class="absolute top-[8px] right-[8px] bg-accent-50/90 hover:bg-accent-50/90"
          />
          {output}
        </Field>
      </div>

      <div>
        <svg
          viewBox="0 0 {outputSize[0]} {outputSize[1]}"
          class="bg-white border border-gray-300 rounded"
        >
          <path d={output} />
        </svg>
      </div>
    </section>
  </div>
</main>
