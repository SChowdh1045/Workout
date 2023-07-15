<script>
  import Modal from "./components/Modal.svelte";
  import Calendar from "./components/Calendar.svelte";

  import "./app.css";

  let show = false;
  let index = -1;

  const day_info = [
    {
      "day":"Monday",
    "video":"https://www.youtube.com/embed/mm47bCaCzpQ"
    },
    {
    "day":"Tuesday",
    "video":""
    },
    {
    "day":"Wednesday",
    "video":""
    },
    {
    "day":"Thursday",
    "video":"https://www.youtube.com/embed/DwOxd4uV0J4"
    },
    {
    "day":"Friday",
    "video":""
    },
    {
    "day":"Saturday",
    "video":""
    },
    {
    "day":"Sunday",
    "video":"https://www.youtube.com/embed/9MYeyEV5u6Q"
    },
  ];

  const handleClick = (i) => {
    show = true;
    index = i;
  }
</script>

<div class="m-3">
  <h1 class="shadow-lg text-5xl text-center py-3 text-white">Testing</h1>

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
    <Modal on:close={() => (show = false)}>
      <div class="mb-8">
        <h2 class="text-2xl underline text-white">{day_info[index].day}</h2>
        <iframe
          class="w-4/5 mx-auto"
          src={day_info[index].video}
          title="1 Second Video"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
          allowfullscreen
        />
        <p class="text-white mt-5">INSTRUCTIONS:</p>

        <div
          class="text-white border-2 border-solid border-green-300 w-10/12 m-auto"
        >
          <p>1. First exercise</p>
          <p>2. Second exercise</p>
          <p>3. Third exercise</p>
        </div>
      </div>
    </Modal>
  {/if}

  <Calendar />
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
