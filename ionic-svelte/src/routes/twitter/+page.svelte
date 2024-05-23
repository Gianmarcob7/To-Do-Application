<style>

</style>

<script lang="ts">
  import Tweet from "$lib/tweet";
  import TweetList from "$lib/tweet_list.svelte"

  let tweets = [
    new Tweet("Bananas are cool"),
    new Tweet("Bananas are cool"),
    new Tweet("Bananas are cool")
  ];

</script>

<TweetList tweets={tweets}></TweetList>
