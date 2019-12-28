<script>
	import { fly } from 'svelte/transition';


	let db;
	let allEntries = [];
	let request = indexedDB.open('water');

  function queryAllEntries() {
		let transaction = db.transaction(['entries'], 'readonly');
		let objectStore = transaction.objectStore('entries');
		const request = objectStore.getAll();

		request.onsuccess = function(event) {
			let res = event.target.result;
			if(res) allEntries = res;
		}
	}

  request.onerror = function(event) {
		console.error('Database error: ' + event.target.errorCode);
	}

	request.onsuccess = function(event) {
		db = event.target.result;
		queryAllEntries()
	}

  const options = { weekday: 'long', month: 'long', day: 'numeric' };

</script>

<main transition:fly={{ x: 200, duration: 750 }} class="container mx-auto text-center min-h-full flex flex-col justify-center">
  <h2 class="text-6xl my-2">History</h2>
  {#each allEntries as {timestamp, quantity}}
    <div class="max-w-sm rounded overflow-hidden shadow-lg bg-purple-400 my-4 text-left">
      <div class="px-6 py-4">
        <div class="font-bold text-xl mb-2">{(new Date(timestamp)).toLocaleDateString('en-EN', options)}</div>
        <p class="text-gray-700 text-base">{quantity}ml</p>
      </div>
    </div>
  {/each}
</main>