<script lang="ts">
	import axios, { type AxiosResponse } from 'axios';
	import dayjs from 'dayjs';
	import { onMount } from "svelte";

    let access_token: string;
    let id_token: string;
	let userInfoRes: AxiosResponse;
    let micropostRes: AxiosResponse;
    let content: string;

    onMount(async () => {

        // セッションストレージからトークンを取得
        access_token = sessionStorage.getItem('access_token') ?? '';
        id_token = sessionStorage.getItem('id_token') ?? '';

        // userinfoエンドポイントにアクセス
        const discoverDoc = JSON.parse(sessionStorage.getItem('discoverDoc') ?? '');
        userInfoRes = await axios.get(discoverDoc.userinfo_endpoint, {headers: {Authorization: `Bearer ${access_token}`}});
        console.log(userInfoRes);

        // 投稿一覧取得APIにアクセス
        micropostRes = await axios.get('https://nfk13r40e6.execute-api.ap-northeast-1.amazonaws.com/api/microposts', {headers: {Authorization: `Bearer ${id_token}`}});
        console.log(micropostRes);
    });

    async function post() {
        // 投稿
        await axios.post('https://nfk13r40e6.execute-api.ap-northeast-1.amazonaws.com/api/microposts', {content: content}, {headers: {Authorization: `Bearer ${id_token}`}});
        // リロード
        micropostRes = await axios.get('https://nfk13r40e6.execute-api.ap-northeast-1.amazonaws.com/api/microposts', {headers: {Authorization: `Bearer ${id_token}`}});
        console.log(micropostRes);
    }
</script>

<h1>投稿</h1>
{#if userInfoRes && micropostRes}
<div style="max-width: 600px; margin: 20px auto;">
    {#each micropostRes.data as micropost}
    <div style="border-bottom: 1px solid #ccc; padding: 10px 0; display: flex; align-items: center;">
        <img style="width:50px; height:50px; border-radius:50%; margin-right:10px;" src={userInfoRes.data.picture} alt="facePicture">
        <div style="flex-grow: 1;">
            <h3 style="margin:0; font-size:18px;">{userInfoRes.data.name}</h3>
            <p style="margin:5px0; font-size:14px; color:#555;">{micropost.content}</p>
            <p style="margin:5px0; font-size:14px; color:#555;">{dayjs(micropost.postedAt).format('YYYY-MM-DD HH:mm:ss')}</p>
        </div>
    </div>
    {/each}
    <div style="padding: 10px 0; display: flex; align-items: center;">
        <div style="flex-grow: 1;">
            <input style="display: inline-block; width: calc(100% - 6em);" type="text" bind:value={content} />
            <button on:click={post}>投稿</button>
        </div>
    </div>
</div>
{/if}
