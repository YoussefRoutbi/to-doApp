<script>
  import { Input, Button, Toaster, toast, Alert, Modal } from '@foxui/core';
  let task = '';
  let Tasks = [];

  let open = false;
  let taskToDelete = null;

  function Add(task) {
    if(task == '' || task.length < 2){
      toast.error('Error', { description: 'Please enter your task' });
      return false;
    }
    Tasks = [...Tasks, { text: task, done: false }];
    toast.success('Success', { description: 'Your task added successfully' });
    return true;
  }

  function Delete(task) {
    taskToDelete = task;
    open = true;
  }

  function confirmDelete() {
    if (taskToDelete) {
      Tasks = Tasks.filter(t => t !== taskToDelete);
      toast.success('Success', { description: 'Task deleted' });
      taskToDelete = null;
    }
    open = false;
  }

  function cancelDelete() {
    taskToDelete = null;
    open = false;
  }

  function Done(task) {
    task.done = !task.done;
    Tasks = [...Tasks];
  }
</script>

<Toaster/>

<main class="h-[100vh] flex flex-col space-y-5 ">
  <h1 class="text-center mt-5 text-3xl font-bold">To Do Application</h1>
  <div class="border-1 border-black"></div>
  <div class="mt-5 flex justify-center space-x-2">
    <Input sizeVariant="lg" placeholder="Enter your task here" bind:value={task} class='w-100'/>
    <Button variant="primary" size="lg" onclick={() => Add(task)}>Add</Button>
  </div>

  <div class="h-[500px] w-[500px] shadow-xl bg-gray-100 p-6 rounded-lg overflow-auto m-auto mt-5">
    <p class="font-semibold text-center mb-2">Tasks</p>
    <div class="border-1 border-black mb-5"></div>

    {#each Tasks as task}
      <div class="flex items-center justify-between space-x-2 mt-2 ml-2">
        <h3 class="font-semibold w-60 overflow-auto">
          {#if task.done}
            <del class="text-gray-400">{task.text}</del>
          {:else}
            {task.text}
          {/if}
        </h3>

        <div>
          <Button variant="blue" size="lg" class='w-20' onclick={() => Done(task)}>Done</Button>
          <Button variant="red" size="lg" class='w-20' onclick={() => Delete(task)}>Delete</Button>

          {#if open}
            <Modal
              bind:open
              title="Do you really want to delete this task?"
              yesButton={{ onclick: confirmDelete }}
              noButton={{ onclick: cancelDelete }}
            />
          {/if}
        </div>
      </div>

      <div class="border-1 border-black/20 mb-5 mt-2 w-full m-auto"></div>
    {/each}
  </div>

</main>
