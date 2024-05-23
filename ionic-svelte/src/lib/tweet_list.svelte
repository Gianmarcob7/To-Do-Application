<style>
    tweet-list{
        display: grid;
        row-gap: 10px
    }
</style>

<script lang="ts">
    import type Tweet from "./tweet"
    import TweetView from "./tweet_view.svelte"
    export let tweets: Tweet[]

    function handleDelete(event) {
        // Remove the tweet from the 'tweets' array
        tweets = tweets.filter(t => t !== event.detail.tweet_to_delete);
    }
</script>
<tweet-list>
    {#each tweets as tweet}
        <TweetView tweet={tweet} on:delete_tweet={handleDelete}></TweetView>
    {/each}
</tweet-list>