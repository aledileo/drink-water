<script>
	import { fade, fly } from 'svelte/transition';

	let waterGlassesCount = 0;
	let qty = 345;


	let db;
	let allEntries = [];
	let request = indexedDB.open('water');
	let totalWaterInMl = 0;
	let totalWaterToday;

	const getQty = (acc, val) => acc + val.quantity;
	$: totalWaterInMl = allEntries.reduce(getQty, 0)
	$: totalWaterToday = allEntries.filter(isToday).reduce(getQty, 0);

	const isToday = function(entry) {
		const givenDate = new Date(entry.timestamp);
		const today = new Date();
		const isSameDay = givenDate.getDate() === today.getDate();
		const isSameMonth = givenDate.getMonth() === today.getMonth();
		const isSameYear = givenDate.getFullYear() === today.getFullYear();
		
		return isSameDay && isSameMonth && isSameYear 
	}

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

	request.onupgradeneeded = function(event) {
		let objectStore;
		db = event.target.result;

		if(!db.objectStoreNames.contains('entries')) {
			objectStore = db.createObjectStore('entries', { keyPath: 'timestamp' });
		}
		
		objectStore.createIndex("timestamp", "timestamp", { unique: true });
		objectStore.createIndex("quantity", "quantity", { unique: false });
	}

	function addEntry() {
		const transaction = db.transaction(['entries'], 'readwrite');
		const store = transaction.objectStore('entries');

		const entry = {
			timestamp: Date.now(),
			quantity: qty
		}

		const request = store.add(entry);
		
		request.onerror = function(event) {
			console.log("Sorry, ", error.target.error.name)
		}

		request.onsuccess = function(event) {
			console.log('Success! Saved entry: ', JSON.stringify(entry, null, 2))
			queryAllEntries()
		}

	}

	$: waterGlassesCount && addEntry()

	function handleClick() {
		waterGlassesCount += 1
	}

	function handleQty(event) {
		qty = event.target.value;
	}

</script>

<main transition:fly="{{ y: 200, duration: 750 }}" class="container mx-auto text-center min-h-full flex flex-col justify-center">
	<h2 class="text-6xl my-2">{totalWaterToday}ml</h2>
	<p class="my-6" style={ totalWaterInMl <= 0 ? 'visibility: hidden' : '' }>You've drinked {totalWaterInMl}ml in total</p>
	<button class="self-center text-center w-3/5 bg-purple-400 py-2 px-4 my-8 rounded-full" on:click={handleClick}>Add water entry ({ qty }ml)</button>
</main>

<style>
</style>