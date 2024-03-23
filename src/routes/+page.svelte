<script lang="ts">
	import axios from "axios";

	async function login() {
		// ディスカバリドキュメントを参照
		const discoverRes = await axios.get('https://accounts.google.com/.well-known/openid-configuration');
		sessionStorage.setItem('discoverDoc', JSON.stringify(discoverRes.data));

		// 認可エンドポイントへのリクエストパラメータ設定
		const params = {
			response_type: 'code',
			client_id: '720487762083-hbm6v2q5loampnkcsicc0d0303lsksom.apps.googleusercontent.com',
			scope: 'openid profile email',
			redirect_uri: 'https://google-login.jtamu-sample-app.link/callback',
			state: Math.random().toString(32).substring(2),
			nonce: Math.random().toString(32).substring(2)
		};

		sessionStorage.setItem('state', params.state);
		sessionStorage.setItem('nonce', params.nonce);

		// 認可エンドポイントへ遷移
		const href = `${discoverRes.data.authorization_endpoint}?response_type=${params.response_type}&client_id=${params.client_id}&scope=${params.scope}&redirect_uri=${params.redirect_uri}&state=${params.state}&nonce=${params.nonce}`;
		location.href = href;
	}

	async function implicitLogin() {
		// ディスカバリドキュメントを参照
		const discoverRes = await axios.get('https://accounts.google.com/.well-known/openid-configuration');
		sessionStorage.setItem('discoverDoc', JSON.stringify(discoverRes.data));

		// 認可エンドポイントへのリクエストパラメータ設定
		const params = {
			response_type: 'token id_token',
			client_id: '720487762083-hbm6v2q5loampnkcsicc0d0303lsksom.apps.googleusercontent.com',
			scope: 'openid profile email',
			redirect_uri: 'https://google-login.jtamu-sample-app.link/implicit/callback',
			state: Math.random().toString(32).substring(2),
			nonce: Math.random().toString(32).substring(2)
		};

		sessionStorage.setItem('state', params.state);
		sessionStorage.setItem('nonce', params.nonce);

		// 認可エンドポイントへ遷移
		const href = `${discoverRes.data.authorization_endpoint}?response_type=${params.response_type}&client_id=${params.client_id}&scope=${params.scope}&redirect_uri=${params.redirect_uri}&state=${params.state}&nonce=${params.nonce}`;
		location.href = href;
	}
</script>

<h1>ログイン</h1>
<p><button on:click={login}>Googleでログイン</button></p>
<p><button on:click={implicitLogin}>Googleでログイン(Implicit)</button></p>
