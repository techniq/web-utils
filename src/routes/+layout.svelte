<script lang="ts">
  import { AppBar, AppLayout, Button, Tooltip, createTheme } from 'svelte-ux';

  import NavMenu from './_NavMenu.svelte';
  import { mdiGithub, mdiTwitter } from '@mdi/js';

  export let data;

  createTheme({
    //
  });
</script>

<AppLayout>
  <nav slot="nav" class="nav h-full">
    <NavMenu />
  </nav>

  <AppBar title="Utilities">
    <div slot="actions" class="flex gap-3">
      <Tooltip title="Open Twitter / X" placement="left" offset={2}>
        <Button
          icon={mdiTwitter}
          href="https://twitter.com/techniq35"
          class="p-2"
          target="_blank"
        />
      </Tooltip>

      <Tooltip title="View repository" placement="left" offset={2}>
        <Button
          icon={mdiGithub}
          href="https://github.com/techniq/web-utils"
          class="p-2"
          target="_blank"
        />
      </Tooltip>
    </div>
  </AppBar>

  <slot />
</AppLayout>

<style lang="postcss">
  @tailwind base;
  @tailwind components;

  :global(body) {
    @apply bg-black/10;
  }

  :global(nav h1) {
    @apply py-2 pl-4 mt-4 text-sm text-gray-200 font-bold bg-black/20 border-t border-b border-white/10;
  }

  :global(nav h2) {
    @apply pt-4 pb-2 pl-4 text-xs text-gray-200 font-bold;
  }

  :global(.AppBar) {
    @apply bg-accent-500 text-white shadow-md;
  }
  :global(nav) {
    @apply bg-neutral-800;
  }

  :global(.NavItem) {
    @apply text-sm text-gray-400 pl-5 py-2 border-l-4 border-transparent;

    &:hover {
      @apply text-white bg-gray-300/10;
    }

    &.is-active {
      @apply text-accent-50 bg-gray-500/10 border-accent-400;
    }
  }

  @tailwind utilities;
</style>
