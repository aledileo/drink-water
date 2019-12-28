<script>
	import Main from './Main.svelte'
	import EntryListPage from './EntryListPage.svelte'

	let defaultPage = true;

	function togglePage() {
		defaultPage = !defaultPage
	}

</script>

<div class="container mx-auto text-center flex flex-col justify-center items-center">
	<button class="rounded-full bg-purple-400 py-2 px-4 w-32" on:click={togglePage}>Toggle Page</button>
</div>
{#if defaultPage}
	<Main/>
{:else}
	<EntryListPage/>
{/if}
