<script>
  import { onMount } from 'svelte'
  import { buildURI, toCSV } from '../core'

  export let separator = ','
  export let filename = 'generatedBy_react-csv.csv'
  export let enclosingCharacter = '"'
  export let uFEFF = true
  export let asyncOnClick = false
  export let target = '_blank'

  export let data
  export let headers = null
  export let onClick = () => console.log('onClick')

  let href = ''
  onMount(() => {
    href = buildURI(data, uFEFF, headers, separator, enclosingCharacter)
  })

  let handleLegacy = event => {
    // If this browser is IE 11, it does not support the `download` attribute
    if (window.navigator.msSaveOrOpenBlob) {
      // Stop the click propagation
      event.preventDefault()

      let blob = new Blob([
        uFEFF ? '\uFEFF' : '',
        toCSV(data, headers, separator, enclosingCharacter),
      ])
      window.navigator.msSaveBlob(blob, filename)

      return false
    }
  }

  let handleAsyncClick = event => {
    const done = proceed => {
      if (proceed === false) {
        event.preventDefault()
        return
      }
      handleLegacy(event)
    }

    onClick(event, done)
  }

  let handleSyncClick = event => {
    const stopEvent = onClick(event) === false
    if (stopEvent) {
      event.preventDefault()
      return
    }
    handleLegacy(event)
  }

  let handleClick = () => {
    return event => {
      if (typeof onClick === 'function') {
        return asyncOnClick ? handleAsyncClick(event) : handleSyncClick(event)
      }
      handleLegacy(event)
    }
  }
</script>

<div>
  <a download={filename} {target} {href} on:click={handleClick()}>
    <slot />
  </a>
</div>
