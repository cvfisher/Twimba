# Twimba

## Overview

This application simulates a basic tweet feed with functionality for liking, retweeting, replying, and adding new tweets. It uses localStorage to persist data across sessions.

---

## Initial Setup

### Import Data and UUID Library

```javascript
import { tweetsData as initialTweetsData } from "./data.js";
import { v4 as uuidv4 } from "https://cdn.jsdelivr.net/npm/uuid@11.0.5/+esm";
```

- `initialTweetsData`: Imported as the default set of tweets.
- `uuidv4`: Generates unique IDs for new tweets.

### LocalStorage Initialization

```javascript
let tweetsData =
	JSON.parse(localStorage.getItem("tweetsData")) || initialTweetsData;
```

- Checks for existing data in `localStorage` under the key `"tweetsData"`.
- Defaults to `initialTweetsData` if no saved data is found.

---

## Core Functions

### Save to LocalStorage

```javascript
function saveToLocalStorage() {
	localStorage.setItem("tweetsData", JSON.stringify(tweetsData));
}
```

- Saves the `tweetsData` array to `localStorage`.

### Event Listener

```javascript
document.addEventListener("click", (e) => {
	if (e.target.dataset.like) {
		handleLikeClick(e.target.dataset.like);
	} else if (e.target.dataset.retweet) {
		handleRetweetClick(e.target.dataset.retweet);
	} else if (e.target.dataset.reply) {
		handleReplyClick(e.target.dataset.reply);
	} else if (e.target.id === "tweet-btn") {
		handleTweetBtnClick();
	}
});
```

- Listens for clicks on interactive elements (like, retweet, reply, and tweet button).

### Handle Like Click

```javascript
function handleLikeClick(tweetId) {
	const targetTweetObj = tweetsData.filter(
		(tweet) => tweet.uuid === tweetId
	)[0];

	if (targetTweetObj.isLiked) {
		targetTweetObj.likes--;
	} else {
		targetTweetObj.likes++;
	}

	targetTweetObj.isLiked = !targetTweetObj.isLiked;
	saveToLocalStorage();
	render();
}
```

- Toggles the `isLiked` state of the tweet and updates the `likes` count.
- Saves changes to `localStorage` and re-renders the feed.

### Handle Retweet Click

```javascript
function handleRetweetClick(tweetId) {
	const targetTweetObj = tweetsData.filter(
		(tweet) => tweet.uuid === tweetId
	)[0];

	if (targetTweetObj.isRetweeted) {
		targetTweetObj.retweets--;
	} else {
		targetTweetObj.retweets++;
	}

	targetTweetObj.isRetweeted = !targetTweetObj.isRetweeted;
	saveToLocalStorage();
	render();
}
```

- Toggles the `isRetweeted` state and updates the `retweets` count.

### Handle Reply Click

```javascript
function handleReplyClick(replyId) {
	document.getElementById(`replies-${replyId}`).classList.toggle("hidden");
}
```

- Shows or hides the replies section of the tweet.

### Handle New Tweet

```javascript
function handleTweetBtnClick() {
	const tweetInput = document.getElementById("tweet-input");
	if (tweetInput.value) {
		tweetsData.unshift({
			handle: `@Scrimba`,
			profilePic: `images/scrimbalogo.png`,
			likes: 0,
			retweets: 0,
			tweetText: tweetInput.value,
			replies: [],
			isLiked: false,
			isRetweeted: false,
			uuid: uuidv4(),
		});
		tweetInput.value = "";
		saveToLocalStorage();
		render();
	}
}
```

- Adds a new tweet to the `tweetsData` array and clears the input field.

---

## Rendering Functions

### Generate Feed HTML

```javascript
function getFeedHtml() {
	let feedHTML = "";

	tweetsData.forEach((tweet) => {
		let likeIconClass = tweet.isLiked ? "liked" : "";
		let retweetIconClass = tweet.isRetweeted ? "retweeted" : "";

		let repliesHtml = "";
		if (tweet.replies.length > 0) {
			tweet.replies.forEach((reply) => {
				repliesHtml += `<div class="tweet-reply">
                    <div class="tweet-inner">
                        <img src="${reply.profilePic}" class="profile-pic">
                        <div>
                            <p class="handle">${reply.handle}</p>
                            <p class="tweet-text">${reply.tweetText}</p>
                        </div>
                    </div>
                </div>`;
			});
		}

		feedHTML += `<div class="tweet">
            <div class="tweet-inner">
                <img src="${tweet.profilePic}" class="profile-pic">
                <div>
                    <p class="handle">${tweet.handle}</p>
                    <p class="tweet-text">${tweet.tweetText}</p>
                    <div class="tweet-details">
                        <span class="tweet-detail">
                            <i class="fa-regular fa-comment-dots" data-reply="${tweet.uuid}"></i>
                            ${tweet.replies.length}
                        </span>
                        <span class="tweet-detail">
                            <i class="fa-solid fa-heart ${likeIconClass}" data-like="${tweet.uuid}"></i>
                            ${tweet.likes}
                        </span>
                        <span class="tweet-detail">
                            <i class="fa-solid fa-retweet ${retweetIconClass}" data-retweet="${tweet.uuid}"></i>
                            ${tweet.retweets}
                        </span>
                    </div>
                </div>
            </div>
            <div class="hidden" id="replies-${tweet.uuid}">
                ${repliesHtml}
            </div>
        </div>`;
	});

	return feedHTML;
}
```

### Render Feed

```javascript
function render() {
	document.getElementById("feed").innerHTML = getFeedHtml();
}

render();
```

- Generates the feed HTML and injects it into the DOM.

---

## Key Features

- **LocalStorage Persistence**: Ensures tweets, likes, and retweets persist across page reloads.
- **Interactive Elements**: Like, retweet, and reply functionalities are dynamically handled.
- **Dynamic Rendering**: Automatically updates the feed when data changes.

---

## Debugging Tips

- Verify `localStorage` updates in the browser's DevTools under the **Application** tab.
- Check the console for errors if functionality doesn't work as expected.
- Ensure the `tweet-input` field is not empty when adding new tweets.

---
