<script>
  import { getContext, onMount, onDestroy } from "svelte"
  export let title
  export const id = Math.random();

  const { styleable, builderStore, componentStore } = getContext("sdk")
  const component = getContext("component")

  const tabStore = getContext("tabStore")

  $:  { $tabStore?.updateTab( id, title ) }
  
  // If in the builder preview, show this Tab Container if a child is selected
  $: {
      if (
        tabStore && 
        $builderStore.inBuilder &&
        $componentStore.selectedComponentPath?.includes($component.id)
      ) {
        $tabStore.selectedTab = id;
      }
    }
  
  onMount ( () =>  { 
    $tabStore?.registerTab( id, title ); 
  })
  onDestroy ( () => {
    $tabStore?.unregisterTab( id ); 
  })
</script>

{#if !tabStore}
  <p> Tab Container must be placed inside a Tabs Component </p>
{:else if $tabStore?.selectedTab === id}
  <div use:styleable={$component.styles} class="container">
      <slot />
  </div>
{/if}

<style>
  p {
    border: 3px dotted red;
    color: red;
    padding: 1.5rem;
    border-radius: 0.25rem;
  }
</style>