<style>
    tweet-view {
        background-color: white;
        box-shadow: 1px 8px 3px 3px black;
        padding: 28px;
    }


</style>

<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import type Tweet from "./tweet"

    const dispatch = createEventDispatcher();
    export let tweet: Tweet;

    let delete_tweet = () => {
        dispatch('delete_tweet', { tweet_to_delete: tweet })
    }
</script>

<tweet-view>
    <!-- Editable input field for the tweet text -->
    <input type="text" bind:value={tweet.text} style="width: 100%; box-sizing: border-box;" placeholder="Edit tweet text..." />
    <button on:click={delete_tweet} style="margin-top: 10px;">x</button>
</tweet-view>
