<script lang="ts">
	import { onMount } from "svelte";

    onMount(() => {
        const fragment = window.location.hash;
        console.log(fragment);
        const keyValueArgs = fragment.substring(1).split('&');
        let fragmentArgs: Record<string, string> = {};
        for (const keyValue of keyValueArgs) {
            const [key, value] = keyValue.split('=');
            fragmentArgs[decodeURIComponent(key)] = decodeURIComponent(value);
        }
        console.log(fragmentArgs);
    });
</script>

<h1>Callback(Implicit)</h1>
