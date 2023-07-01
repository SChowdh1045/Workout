<script>
  import Modal from "./components/Modal.svelte";
  import "./app.css";

  let show = false;
  let index = -1;

  const day_info = [
    {
    "day":"Monday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Tuesday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Wednesday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Thursday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Friday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Saturday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
    {
    "day":"Sunday",
    "video":"https://www.youtube.com/embed/MvsAesQ-4zA"
    },
  ];

  const handleClick = (i) => {
    show = true;
    index = i;
  }

  const handleClose = () => {
    show = false;
  }
</script>


<div class="m-3">
  <h1 class="shadow-lg text-5xl text-center py-3">Testing</h1>

  <ul class="flex justify-evenly my-10">
    <li class="btn btn-blue" on:click={() => handleClick(0)}>Monday</li>
    <li class="btn btn-blue" on:click={() => handleClick(1)}>Tuesday</li>
    <li class="btn btn-blue" on:click={() => handleClick(2)}>Wednesday</li>
    <li class="btn btn-blue" on:click={() => handleClick(3)}>Thursday</li>
    <li class="btn btn-blue" on:click={() => handleClick(4)}>Friday</li>
    <li class="btn btn-blue" on:click={() => handleClick(5)}>Saturday</li>
    <li class="btn btn-blue" on:click={() => handleClick(6)}>Sunday</li>
  </ul>


  {#if show}
    <Modal on:close={() => handleClose()}>
      <div>
        <h2 class="text-2xl underline">{day_info[index].day}</h2>
        <iframe class="w-4/5 mx-auto" src={day_info[index].video} title="1 Second Video" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
        <p>INSTRUCTIONS:</p>
      </div>
    </Modal>
  {/if}

  <h1 class="text-center mt-6">[Insert Calendar]</h1>
</div>


<style>
  .btn {
    @apply font-bold py-2 px-4 rounded;
  }
  .btn-blue {
    @apply bg-blue-500 text-white;
  }
  .btn-blue:hover {
    @apply bg-blue-700 cursor-pointer;
  }
</style>