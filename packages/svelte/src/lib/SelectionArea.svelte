<script>
   import {
      onDestroy,
      createEventDispatcher
   } from 'svelte';
   import VanillaSelectionArea from '@viselect/vanilla';

   const dispatch = createEventDispatcher();

   export let selectionAreaClass;
   export let selectionContainerClass;
   export let container;
   export let document;
   export let selectables;
   export let behaviour;
   export let features;

   export let onBeforeStart;
   export let onBeforeDrag;
   export let onStart;
   export let onMove;
   export let onStop;

   let cls = ''; export { cls as class };

   let boundaries;
   let selectionArea;

   $: {
      if (boundaries) {
         if (selectionArea) {
            selectionArea.destroy();
         }
         
         selectionArea = new VanillaSelectionArea({
            boundaries,
            selectionAreaClass,
            selectionContainerClass,
            container,
            document,
            selectables,
            behaviour,
            features
         });
         selectionArea.on('beforestart', (event) => {
            dispatch('beforestart', event);
            return onBeforeStart && onBeforeStart(event);
         }).on('beforedrag', (event) => {
            dispatch('beforedrag', event);
            return onBeforeDrag && onBeforeDrag(event);
         }).on('start', (event) => {
            dispatch('start', event);
            onStart && onStart(event);
         }).on('move', (event) => {
            dispatch('move', event);
            onMove && onMove(event);
         }).on('stop', (event) => {
            dispatch('stop', event);
            onStop && onStop(event);
         });
      }
   }
   
   onDestroy(() => selectionArea && selectionArea.destroy());
</script>

<div
   bind:this={boundaries}
   class={cls}
   >
   <slot></slot>
</div>
