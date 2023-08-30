<script lang="ts">
  import svgpath from 'svgpath';
  import { AppBar, CopyButton, Field, TextField, autoHeight, cls } from 'svelte-ux';

  let input = '';
  let output: string | null = null;
  let inputSize = [512, 512];
  let outputSize = [24, 24];
  let precision = 2;
  let translate = [0, 0];

  // Scale based on input/input viewport ratio differences
  $: scaleRatio = Math.max(...outputSize) / Math.max(...inputSize);
  // let scaleRatio = 1.5;
  // 0.046875

  // Center based on input/output viewport ratio differences
  // $: translate =
  //   inputSize[0] < inputSize[1]
  //     ? [((inputSize[1] - inputSize[0]) * scaleRatio) / 2, 0]
  //     : inputSize[0] > inputSize[1]
  //     ? [0, ((inputSize[0] - inputSize[1]) * scaleRatio) / 2]
  //     : [0, 0];
  // Center based on scale ratio
  // $: translate = [(24 * scaleRatio - 24) / -2, (24 * scaleRatio - 24) / -2];

  /*
    TODO:
    - [ ] Support specifying the precision
    - [ ] Support converting to all relative or absolute commands (.rel() and .abs())
    - [ ] Support translating (and maybe simplify setting top left)
    - [ ] Rotate
    - [ ] Unshort
    - [ ] Unarc
    - [ ] Quick presets for viewboxes (512x512, 24x24, etc)
    - [ ] Attempt to detect viewbox?

    */

  /*
   See also:
    - https://github.com/fontello/svgpath
    - https://github.com/aydos/svgpath
    - https://aydos.com/svgedit/
    - https://github.com/thednp/svg-path-commander
      - https://thednp.github.io/svg-path-commander/
    - https://github.com/kpym/SVGPathy
      - https://kpym.github.io/SVGPathy/
    - https://yqnn.github.io/svg-path-editor/
    - https://lea.verou.me/blog/2019/05/utility-convert-svg-path-to-all-relative-or-all-absolute-commands/
    - https://codepen.io/anthonydugois/pen/EKzzmV
  */

  $: try {
    output = input
      ? svgpath(input)
          .scale(scaleRatio)
          .translate(...translate)
          .round(precision)
      : null;
  } catch {
    output = null;
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

      <TextField
        placeholder="Enter SVG path data"
        bind:value={input}
        multiline
        actions={(node) => [autoHeight(node)]}
      />

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

        <span class="text-gray-500 text-sm font-medium">Translate</span>
        <TextField bind:value={translate[0]} type="decimal" dense class="w-20" />
        x
        <TextField bind:value={translate[1]} type="decimal" dense class="w-20" />

        <span class="text-gray-500 text-sm font-medium">Scale</span>
        <TextField bind:value={scaleRatio} type="decimal" dense class="w-20" />
      </div>

      <div>
        <Field class="relative">
          {#if output}
            <CopyButton
              value={output}
              color="accent"
              size="sm"
              class="absolute top-[8px] right-[8px] bg-accent-50/90 hover:bg-accent-50/90"
            />
          {/if}
          <span class={cls('text-sm', !output && 'text-black/30')}>
            {output ?? 'Missing input'}
          </span>
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
