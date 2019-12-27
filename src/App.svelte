<script>

	let name = 'Ale'
	let waterGlassesCount = 0;
	let qty = 345;


	let db;
	let allEntries = [];
	let request = indexedDB.open('water');

	request.onerror = function(event) {
		console.error('Database error: ' + event.target.errorCode);
	}

	request.onsuccess = function(event) {
		db = event.target.result;
		let transaction = db.transaction(['entries'], 'readonly');
		let objectStore = transaction.objectStore('entries');
		let cursor = objectStore.openCursor();

		cursor.onsuccess = function(event) {
			let res = event.target.result;
			if (res) {
				allEntries.push(res.value);
				res.continue();
			}
		}
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

	function addEntry(selectedQty) {
		const transaction = db.transaction(['entries'], 'readwrite');
		const store = transaction.objectStore('entries');

		const entry = {
			timestamp: Date.now(),
			quantity: selectedQty
		}

		const request = store.add(entry);
		
		request.onerror = function(event) {
			console.log("Sorry, ", error.target.error.name)
		}

		request.onsuccess = function(event) {
			console.log('Success! Saved entry: ', JSON.stringify(entry, null, 2))
		}
	}

	function handleClick() {
		waterGlassesCount += 1
		addEntry(qty)
	}

	function handleQty(event) {
		qty = event.target.value;
	}

</script>

<main>
	<h1>Hello {name}!</h1>
	<p>You've drinked {waterGlassesCount} glasses of water so far!</p>
	<button on:click={handleClick}>Add glass of water</button>
	<!-- <input type="number" on:change={handleQty}> -->

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>