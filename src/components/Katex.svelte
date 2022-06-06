<script lang="ts">
  import katex from 'katex';

  export let formula: string;
  export let inline = false;

  let html = katex.renderToString(formula)
</script>

{#if inline}
  <span>{@html html}</span>
{:else}
  <div>{@html html}</div>
{/if}

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.15.6/dist/katex.min.css" integrity="sha384-ZPe7yZ91iWxYumsBEOn7ieg8q/o+qh/hQpSaPow8T6BwALcXSCS6C6fSRPIAnTQs" crossorigin="anonymous">
