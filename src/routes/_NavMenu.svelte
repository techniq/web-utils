<script lang="ts">
  import { mdiHome, mdiVectorPolyline } from '@mdi/js';

  import { NavItem } from 'svelte-ux';

  import { page } from '$app/stores';
</script>

<NavItem text="Home" icon={mdiHome} path="/" currentUrl={$page.url} class="mt-2" />

<h2>SVG</h2>

<NavItem
  text="Path viewport"
  icon={mdiVectorPolyline}
  path="/pathViewport"
  currentUrl={$page.url}
/>
