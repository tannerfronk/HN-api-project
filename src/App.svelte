<script>
	import moment from 'moment'

	let storiesURL = 'https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty' 
	let stories = []
	let story = getStories()
	let individualStories = getIndividualStories()

	moment.updateLocale('en', null) //sets the default local for moment.js

	async function getStories(){
		const res = await fetch(storiesURL)
		let storyIDs = await res.json()
		for(let i=0; i<30; i++){
			stories.push(storyIDs[i])
		}
		return stories;
	}
	async function getIndividualStories(){
		await story
		let storyObj = []
		for(let i = 0; i<30; i++){
			const res = await fetch(`https://hacker-news.firebaseio.com/v0/item/${stories[i]}.json`)
			let thisStory = await res.json()
			let storyTime = await thisStory.time * 1000
			let newTime = new Date(storyTime)
			let relativeTime = moment(newTime).fromNow()
			let comments = "discuss"
			if(thisStory.kids != null){
				comments = thisStory.kids.length + " comments"
			}
			let isolatedURL = ""
			if(thisStory.url != null){
				let splitURL = thisStory.url.split("/")
				isolatedURL = " ("+splitURL[2]+")"
			}
			storyObj.push({story: thisStory, time: relativeTime, commentsNum: comments, url: isolatedURL})
		}
		return storyObj
	}



</script>

<main>
		<header>
			<div class="menu">
				<img src="images/y18.gif" alt="hacker news logo">
				<p><b>Hacker News</b></p>
				<p>new | </p>
				<p>past | </p>
				<p>comments | </p>
				<p>ask | </p>
				<p>how | </p>
				<p>jobs | </p>
				<p>submit</p>
			</div>
			<div>
				<span>login</span>
			</div>
		</header>
<section class="storySection">

	{#await individualStories}
		<p>Loading Stories...</p>
	{:then storyObj} 
		{#each storyObj as single, i}
			<div class="stories">
				<div class="storyNum"><span>{i+1}. </span><span id="upvote">&#x25B2;</span></div>
				<div class="storiesContent">
					<h2> <a href={single.story.url}>{single.story.title}</a><span> {single.url}</span></h2>
					<p>{single.story.score} points by {single.story.by} {single.time} | hide | {single.commentsNum}</p>
				</div>
			</div>
		{/each}
	{/await}

</section>







</main>

<style>
	main {
		padding: 1em;
		margin: 0 auto;
	}
	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}
	h2 {
		font-size: 14px;
		padding: 0;
		margin: 0;
		font-weight: 500;
	}
	header {
		display: flex;
		flex-direction: row;
		background-color: #ff6600;
		width: 100%;
		justify-content: space-between;
	}
	header .menu {
		display: flex;
		flex-direction: row;
	}
	header p {
		padding-left: 5px;
	}
	header span {
		margin-right: 6px;
		color: black;
	}
	header img {
		width: 18px;
		height: 18px;
		border: 1px solid white;
		margin: 2px;
		margin-right: 2px;
	}
	p {
		margin: 0;
		padding: 0;
	}
	.storySection {
		padding-top: 10px;
		background-color: rgb(246, 246, 239);
	}
	#upvote {
		padding-left: 2px;
		margin-lefT: 0;
		font-size: 12px;
		padding-right: 2px;
	}
	#upvote:hover {
		cursor: pointer;
	}
	.stories {
		display: flex;
	}
	.storyNum {
		font-size: 14px;
		display: flex;
		flex-wrap: nowrap;
	}
	.storiesContent {
		display: flex;
		flex-direction: column;
	}
	.stories p {
		font-size: 12px;
		color: #828282;
		padding-bottom: 5px;
	}
	.stories span {
		margin-left: 10px;
		display:inline-block;
	}
	.stories div {
		display: flex;
	}
	a {
		text-decoration: none;
		color: #000000;
	}
	a:visited, span {
		color: #828282;
	}
	a:hover {
		text-decoration: underline;
	}

	@media (min-width: 690px) {
		main {
			max-width: 80%;
		}
	}
	@media (max-width: 690px) {

	}
</style>