<script lang="ts">
  import { mdiHome, mdiPalette, mdiVectorPolyline } from '@mdi/js';

  import { NavItem } from 'svelte-ux';

  import { page } from '$app/stores';
</script>

<NavItem text="Home" icon={mdiHome} path="/" currentUrl={$page.url} class="mt-2" />

<h2>Design</h2>

<NavItem text="Color" icon={mdiPalette} path="/color" currentUrl={$page.url} />

<h2>SVG</h2>

<NavItem
  text="Path viewport"
  icon={mdiVectorPolyline}
  path="/pathViewport"
  currentUrl={$page.url}
/>
