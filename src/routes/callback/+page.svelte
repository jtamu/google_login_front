<script lang="ts">
	import { error } from '@sveltejs/kit';
	import { onMount } from 'svelte';
	let queryParams;

	onMount(() => {
		const params = new URLSearchParams(window.location.search);
		queryParams = Object.fromEntries(params.entries());
		console.log(queryParams);

		const sessionState = sessionStorage.getItem('state');
		console.log(sessionState);

		if (queryParams.state != sessionState) {
			error(401, { message: 'Invalid state parameter.' });
		}
	});
</script>

<h1>Callback</h1>
