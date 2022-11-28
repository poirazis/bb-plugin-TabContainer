<script>
  import { getContext, onMount, onDestroy, setContext } from "svelte"
  export let title
  export let icon 
  export const id = Math.random();

  const { styleable, builderStore, componentStore } = getContext("sdk")
  const component = getContext("component")
  const tabStore = getContext("tabStore")

  const nested = getContext("topLevel")
  if (!nested) setContext("topLevel", 1)

  $: {
      $tabStore?.updateTab( id, title, icon ) 
  }
  
  // If in the builder preview, show this Tab Container if a child is selected
  $: {
    if (
      !nested && 
      tabStore && 
      $builderStore.inBuilder &&
      $componentStore.selectedComponentPath?.includes($component.id)
    ) {
      $tabStore.selectedTab = id;
    }
  } 
  
  onMount ( () =>  { 
    if (!nested)
    {
      $tabStore?.registerTab( id, title, icon );
    }
  })

  onDestroy ( () => {
    $tabStore?.unregisterTab( id ); 
  })
</script>

{#if !tabStore}
  <p> Tab Container must be placed inside a Tabs Component. </p>
{:else if nested}
  <p> Tab Containers cannot be nested. They need to be top level in a Tabs Component. </p>
{:else if $tabStore?.selectedTab === id }
  <div use:styleable={$component.styles} class="container">
      <slot/> 
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