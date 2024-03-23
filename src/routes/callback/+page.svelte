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

		const userInfoRes = await axios.get('https://openidconnect.googleapis.com/v1/userinfo', {headers: {Authorization: `Bearer ${res.data.access_token}`}});
		console.log(userInfoRes);
	});
</script>

<h1>Callback</h1>
