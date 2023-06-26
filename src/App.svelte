<script>
  import Modal from "./components/Modal.svelte";
  let show = false;

</script>

<h1>Hello World!</h1>

<ul style="list-style-type: none;">
  <li on:click={() => show=true}>Monday</li>
  <li>T</li>
  <li>W</li>
  <li>Th</li>
  <li>F</li>
  <li>Sat</li>
  <li>Sun</li>
</ul>

{#if show}
  <Modal on:close={() => show=false}>
      <h3>Monday</h3>
      <a href="https://www.youtube.com/watch?v=MvsAesQ-4zA&ab_channel=Chisss" target="_blank">Video 4 Monday</a>
  </Modal>
{/if}