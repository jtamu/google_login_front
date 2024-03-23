<script lang="ts">
	import { error } from "@sveltejs/kit";
	import axios, { type AxiosResponse } from "axios";
	import { onMount } from "svelte";
	let userInfoRes: AxiosResponse;

    onMount(async () => {
        const fragment = window.location.hash;
        const keyValueArgs = fragment.substring(1).split('&');
        let fragmentArgs: Record<string, string> = {};
        for (const keyValue of keyValueArgs) {
            const [key, value] = keyValue.split('=');
            fragmentArgs[decodeURIComponent(key)] = decodeURIComponent(value);
        }
        console.log(fragmentArgs);

		// 送られてきたstateが一致しているか確認
		const sessionState = sessionStorage.getItem('state');

		if (fragmentArgs.state != sessionState) {
			error(401, { message: 'Invalid state parameter.' });
		}

        // userinfoエンドポイントにアクセス
		const discoverDoc = JSON.parse(sessionStorage.getItem('discoverDoc') ?? '');
		userInfoRes = await axios.get(discoverDoc.userinfo_endpoint, {headers: {Authorization: `Bearer ${fragmentArgs.access_token}`}});
		console.log(userInfoRes);
    });
</script>

{#if userInfoRes}
	<h1>{userInfoRes.data?.name}</h1>
	<img src={userInfoRes.data?.picture} alt="facePicture" />
{:else}
	<h1>ログインしています...</h1>
{/if}
