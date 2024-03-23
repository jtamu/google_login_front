<script lang="ts">
	import axios, { type AxiosResponse } from "axios";
	import { onMount } from "svelte";
	let userInfoRes: AxiosResponse;

    onMount(async () => {
        const fragment = window.location.hash;
        console.log(fragment);
        const keyValueArgs = fragment.substring(1).split('&');
        let fragmentArgs: Record<string, string> = {};
        for (const keyValue of keyValueArgs) {
            const [key, value] = keyValue.split('=');
            fragmentArgs[decodeURIComponent(key)] = decodeURIComponent(value);
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
