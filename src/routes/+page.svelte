<script lang="ts">
	function login() {
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

		const href = `https://accounts.google.com/o/oauth2/v2/auth?response_type=${params.response_type}&client_id=${params.client_id}&scope=${params.scope}&redirect_uri=${params.redirect_uri}&state=${params.state}&nonce=${params.nonce}`;
		location.href = href;
	}
</script>

<h1>ログイン</h1>
<p><button on:click={login}>Googleでログイン</button></p>
