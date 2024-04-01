<script lang="ts">
	import { goto } from '$app/navigation';
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

		// nonceの検証
		const sessionNonce = sessionStorage.getItem('nonce');
		const payload = JSON.parse(decodeURIComponent(atob(res.data.id_token.split('.')[1])));
		if (sessionNonce != payload.nonce) {
			error(401, { message: 'Invalid nonce parameter.' });
		}

		// トークンをセッションストレージに保存
		sessionStorage.setItem('access_token', res.data.access_token);
		sessionStorage.setItem('id_token', res.data.id_token);

		// userinfoエンドポイントにアクセス
		const discoverDoc = JSON.parse(sessionStorage.getItem('discoverDoc') ?? '');
		userInfoRes = await axios.get(discoverDoc.userinfo_endpoint, {headers: {Authorization: `Bearer ${res.data.access_token}`}});
		console.log(userInfoRes);
	});
</script>

{#if userInfoRes}
	<h1>{userInfoRes.data?.name}</h1>
	<img src={userInfoRes.data?.picture} alt="facePicture" />
	<button on:click={() => goto("/microposts")}>投稿</button>
{:else}
	<h1>ログインしています...</h1>
{/if}
