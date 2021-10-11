---
layout: post
title: "A new comments system for my static Jekyll site"
permalink: blog/static-site-comments-using-github-issues-api
categories:
  - Jekyll
comments_id: 5
---

I love WordPress, but I strongly believe there's a right tool for every job. This site is not a WordPress site. It's built on [Jekyll](https://jekyllrb.com/) and hosted on [Github Pages](https://pages.github.com/) for free.  
I write my posts using simple Markdown and it's just fast and convenient.  
However, what I always found annoying is the lack of a comment system. Until now I was using [disqus](https://disqus.com/) for comments on this site, but I never really liked it. It was a last-resort solution. One I hated because Disqus loads a lot of assets and a lot of trackers.

Those who know me personally, know that I am concerned with online privacy and web-sustainability. I don't even have analytics on my sites simply because I consider the policies of tech giants like Google abhorrent, and the fact that almost all sites load all these extra JS assets is insanely costly for the environment.

Disqus in particular was loading assets and trackers from Google, Facebook, Twitter and its own things. So I could no longer stand the thought of using Disqus, a custom solution had to be implemented.
Today I finally managed to do that and implement my new comments system.

Since this site is hosted on github pages there is already a repository. And since most of what I write is tech-related, it's safe to assume that people reading these posts have an account on GitHub. So leveraging the [GitHub Issues API](https://developer.github.com/v3/issues/), whenever I want to enable comments on a post I just create a new issue on the repository, and then in the headers of my post just add this:
```
comments_id: 5
```
Of course `5` in the above example is just the ID of the issue in the repository and each tickets has its own.  
When someone wants to add a comment on a post, they click the link and they get taken to the corresponding issue on the repository where they can leave their comment.  As soon as a comment is posted it appears on the page.

## The Code

I created a new `comments.html` file in my `_includes` folder, and wrote this in there:

```html
<script>
const GH_API_URL = 'https://api.github.com/repos/aristath/aristath.github.com/issues/{{ page.comments_id }}/comments?per_page=100';

let request = new XMLHttpRequest();
request.open( 'GET', GH_API_URL, true );
request.onload = function() {
	if ( this.status >= 200 && this.status < 400 ) {
		let response = JSON.parse( this.response );

		for ( var i = 0; i < response.length; i++ ) {
			document.getElementById( 'gh-comments-list' ).appendChild( createCommentEl( response[ i ] ) );
		}

		if ( 0 === response.length ) {
			document.getElementById( 'no-comments-found' ).style.display = 'block';
		}
	} else {
		console.error( this );
	}
};

function createCommentEl( response ) {
	let user = document.createElement( 'a' );
	user.setAttribute( 'href', response.user.url.replace( 'api.github.com/users', 'github.com' ) );
	user.classList.add( 'user' );

	let userAvatar = document.createElement( 'img' );
	userAvatar.classList.add( 'avatar' );
	userAvatar.setAttribute( 'src', response.user.avatar_url );

	user.appendChild( userAvatar );

	let commentLink = document.createElement( 'a' );
	commentLink.setAttribute( 'href', response.html_url );
	commentLink.classList.add( 'comment-url' );
	commentLink.innerHTML = '#' + response.id + ' - ' + response.created_at;

	let commentContents = document.createElement( 'div' );
	commentContents.classList.add( 'comment-content' );
	commentContents.innerHTML = response.body;

	let comment = document.createElement( 'li' );
	comment.setAttribute( 'data-created', response.created_at );
	comment.setAttribute( 'data-author-avatar', response.user.avatar_url );
	comment.setAttribute( 'data-user-url', response.user.url );

	comment.appendChild( user );
	comment.appendChild( commentContents );
	comment.appendChild( commentLink );

	return comment;
}
request.send();
</script>

<hr>

<div class="github-comments">
	<h2>Comments</h2>
	<ul id="gh-comments-list"></ul>
	<p id="no-comments-found">No comments found for this article. Comments posted before 2019-09-15 are no longer available due to a system migration since I no longer use Disqus for comments.</p>
	<p id="leave-a-comment">Join the discussion for this article on <a href="https://github.com/aristath/aristath.github.com/issues/{{ page.comments_id }}">this ticket</a>. Comments appear on this page instantly.</p>
</div>
```

Depending on the number of requests you have on your site you may want to add thes at the end of the URL in the `GH_API_URL` constant in the above script:
```
?client_id=MY_CLIENT_ID&client_secret=MY_CLIENT_SECRET
```
To get the client-ID and client-secret you'll have to create a new application from your [GitHub Profile > Developer Settings > OAuth Apps](https://github.com/settings/applications/new). Don't worry, it only requires an application name and a URL. You can use the same URL for the callback, so it only takes 10-20 seconds to create your app and get the keys.

You can see how that file is then loaded on the template for posts [here](https://github.com/aristath/aristath.github.com/blob/f1b80c1202ed9edd3d5b8b9ba7cf15f347d4bfc6/_layouts/post.html#L22-L24).

The logic is simple: We make a request to the GitHub API, we get the comments from the issue and render them. The code may seem a bit long but that's only because I'm using Vanilla JS instead of libraries like jQuery, so elements have to be created individually and then added to the DOM. The above code will get the user avatar, create a link to the user's profile, add the comment, add a link to the comment, the date and everything a comment usually has.

It's fast, there's no privacy concerns, no cookies, no nothing. And as an additional bonus you can just style it the way you want it.

I hope that helps someone out there, it took me quite a while to figure this one out.
