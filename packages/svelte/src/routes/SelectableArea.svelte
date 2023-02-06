<script>
   import SelectionArea from '$lib/SelectionArea.svelte';
   
   export let boxes = 0;
   let cls = ''; export { cls as class };

   const nodeToBoxMap = new Map();
   
   const selectable = (node, { index }) => {
      
      nodeToBoxMap.set(node, index);
      return { destroy: () => nodeToBoxMap.delete(node) };
   };
   
   let selected = new Set();
   
   const onStart = ({ detail: { event, selection } }) => {
      
      if (!event?.ctrlKey && !event?.metaKey) {
         selection.clearSelection();
         selected.clear();
      }
   };

   const onMove = ({ detail: { store: { changed: { added, removed } } } }) => {
   
      added.forEach((node) => selected.add(nodeToBoxMap.get(node)));
      removed.forEach((node) => selected.delete(nodeToBoxMap.get(node)));
      selected = selected;
   };
</script>

<SelectionArea class={`container ${cls}`}
               on:start={onStart}
               on:move={onMove}
               selectables=".selectable">
   
   {#each new Array(boxes).fill() as _, index}
      <div 
         class="selectable"
         class:selected={selected.has(index)}
         use:selectable={{ index }}
      />
   {/each}
   
</SelectionArea>
