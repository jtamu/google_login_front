<script lang="ts">
	import { error } from '@sveltejs/kit';
	import axios from 'axios';
	import { onMount } from 'svelte';
	let queryParams;

	onMount(async () => {
		const params = new URLSearchParams(window.location.search);
		queryParams = Object.fromEntries(params.entries());

		const sessionState = sessionStorage.getItem('state');

		if (queryParams.state != sessionState) {
			error(401, { message: 'Invalid state parameter.' });
		}

		const res = await axios.post('https://nfk13r40e6.execute-api.ap-northeast-1.amazonaws.com/api/get_token', {
			code: queryParams.code,
		});
		console.log(res);
	});
</script>

<h1>Callback</h1>
