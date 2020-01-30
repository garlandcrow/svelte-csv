<script>
  import { onMount } from 'svelte'
  import { buildURI } from '../core'

  export let separator = ','
  export let enclosingCharacter = '"'
  export let uFEFF = true
  export let target = '_blank'

  export let data
  export let headers = null
  export let specs = null
  export let replace = null

  onMount(() => {
    window.open(
      buildURI(data, uFEFF, headers, separator, enclosingCharacter),
      target,
      specs,
      replace,
    )
  })
</script>
