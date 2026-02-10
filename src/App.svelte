<script>
  let file = null;
  let content = "";
  let lines = [];
  let tail = [];
  let filtered = "";
  let error = "";
  let showAlert = false;

  function handleFile(f) {
    if (!f) return;

    file = f;
    const reader = new FileReader();

    reader.onload = () => {
      content = reader.result;
      lines = content.split("\n");
      tail = lines.slice(-10);
      filtered = "";
      showAlert = false;
      error = "";
    };

    reader.onerror = () => {
      error = "Failed to read file.";
    };

    reader.readAsText(f);
  }

  function onDrop(e) {
    e.preventDefault();
    handleFile(e.dataTransfer.files[0]);
  }

  function filterFile() {
    if (!content) return;
    const uniqueEmails = Array.from(new Set(lines.map(l => l.trim()).filter(l => l)));
    filtered = uniqueEmails.join("\n");
    tail = uniqueEmails.slice(-10);
    showAlert = true;
  }

  function downloadFile() {
    const blob = new Blob([filtered], { type: "text/plain" });
    const url = URL.createObjectURL(blob);

    const a = document.createElement("a");
    a.href = url;
    a.download = "filtered_" + (file?.name || "file.txt");
    a.click();

    URL.revokeObjectURL(url);
  }
</script>

<div class="min-h-screen bg-gradient-to-br from-gray-900 via-black to-gray-800 text-gray-200
            flex flex-col items-center p-6 animate-gradient">

  <h1 class="text-3xl font-bold mb-6">Email Filter App</h1>

  {#if error}
    <div class="bg-red-600/20 border border-red-600 text-red-300 px-4 py-2 rounded mb-4">
      {error}
    </div>
  {/if}

  {#if showAlert}
    <div class="bg-green-600/20 border border-green-600 text-green-300 px-4 py-2 rounded mb-4 transition">
      Filter finished! You can now download your file.
    </div>
  {/if}

  <div
    class="w-full max-w-4xl border-2 border-dashed border-gray-600 rounded-lg
           p-6 text-center hover:border-indigo-500 transition"
    on:dragover|preventDefault
    on:drop={onDrop}
  >
    <p class="mb-2">Drag & drop a file here</p>
    <input type="file" class="hidden" id="fileInput" on:change={(e)=>handleFile(e.target.files[0])}>
    <label for="fileInput" class="cursor-pointer text-indigo-400 hover:underline">
      or click to select
    </label>
  </div>

  {#if content}
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 w-full max-w-6xl mt-6">
      <div class="bg-black/40 rounded p-4 overflow-auto h-96">
        <h2 class="font-semibold mb-2">Original Content</h2>
        <pre class="text-sm whitespace-pre-wrap">{content}</pre>
      </div>

      <div class="bg-black/40 rounded p-4 h-96 flex flex-col">
        <h2 class="font-semibold mb-2">Details</h2>
        <p class="mb-2">Lines: <span class="text-indigo-400">{lines.length}</span></p>
        <h3 class="font-semibold mt-4 mb-1">Tail (last 10 lines)</h3>
        <pre class="text-sm whitespace-pre-wrap flex-1 overflow-auto">
{tail.join("\n")}
        </pre>
      </div>
    </div>

    <div class="flex gap-4 mt-6">
      <button
        class="px-6 py-2 bg-indigo-600 hover:bg-indigo-700 rounded transition"
        on:click={filterFile}
      >
        Filter
      </button>

      {#if filtered}
        <button
          class="px-6 py-2 bg-green-600 hover:bg-green-700 rounded transition"
          on:click={downloadFile}
        >
          Download
        </button>
      {/if}
    </div>
  {/if}
</div>

<style>
  @keyframes gradient {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }

  .animate-gradient {
    background-size: 300% 300%;
    animation: gradient 10s ease infinite;
  }
</style>
