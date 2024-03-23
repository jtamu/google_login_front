<script lang="ts">
	import { error } from '@sveltejs/kit';
	import axios, { type AxiosResponse } from 'axios';
	import { onMount } from 'svelte';
	let queryParams;
	let userInfoRes: AxiosResponse;

	onMount(async () => {
		// コールバックエンドポイントにリダイレクトしてきた際のクエリパラメータを取得
		const params = new URLSearchParams(window.location.search);
		queryParams = Object.fromEntries(params.entries());

		// 送られてきたstateが一致しているか確認
		const sessionState = sessionStorage.getItem('state');

		if (queryParams.state != sessionState) {
			error(401, { message: 'Invalid state parameter.' });
		}

		// バックエンドからトークン取得
		const res = await axios.post('https://nfk13r40e6.execute-api.ap-northeast-1.amazonaws.com/api/get_token', {
			code: queryParams.code,
		});

		// userinfoエンドポイントにアクセス
		const discoverDoc = JSON.parse(sessionStorage.getItem('discoverDoc') ?? '');
		userInfoRes = await axios.get(discoverDoc.userinfo_endpoint, {headers: {Authorization: `Bearer ${res.data.access_token}`}});
		console.log(userInfoRes);
	});
</script>

{#if userInfoRes}
	<h1>{userInfoRes.data?.name}</h1>
	<img src={userInfoRes.data?.picture} alt="facePicture" />
{:else}
	<h1>ログインしています...</h1>
{/if}
