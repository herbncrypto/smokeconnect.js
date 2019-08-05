[![npm](https://img.shields.io/npm/v/steemconnect.svg)](https://www.npmjs.com/package/smokeconnect)
![npm](https://img.shields.io/npm/dm/steemconnect.svg)
![CircleCI](https://img.shields.io/circleci/project/github/bonustrack/steemconnect.js.svg)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/bonustrack/steemconnect.js/master/LICENSE)

# SmokeConnect.js

The official SmokeConnect JavaScript SDK.

## Getting started

To install and run SmokeConnect.js, follow this quick start guide

### Install

SmokeConnect.js was designed to work both in the browser and in Node.js.

#### Node.js
To install SmokeConnect.js on Node.js, open your terminal and run:
```
npm i smokeconnect --save
```

#### Browser

You can create an index.html file and include SmokeConnect.js with:

```html
<script src="https://cdn.jsdelivr.net/npm/smokeconnect"></script>
```

### Usage

For general information about SteemConnect and setting up your app you can checkout the developer documentation.

**[Developers documentation](https://beta.smokeconnect.com/developers)**

## SDK methods

### Init client
Call the Client() method when your app first loads to init the SDK:
```
var smokeconnect = require('smokeconnect');

var client = new smokeconnect.Client({
  app: 'staging.app',
  callbackURL: 'https://demo.smokeconnect.com',
  scope: ['vote', 'comment']
});
```
Parameters:
- __app__: This is the name of the app that was registered in the SmokeConnect V2 dashboard
- __callbackURL__: This is the URL that users will be redirected to after interacting with SmokeConnect. It must be listed in the "Redirect URI(s)" list in the app settings EXACTLY the same as it is specified here
- __accessToken__: If you have an oauth2 access token for this user already you can specify it here, otherwise you can leave it and set it later using steemconnect.setAccessToken(accessToken).
- __scope__: This is a list of operations the app will be able to access on the user's account. For a complete list of scopes see: [https://github.com/herbncrypto/smokeconnect/wiki/OAuth-2#scopes](https://github.com/herbncrypto/smokeconnect/wiki/OAuth-2#scopes)

### Universal log in

This method trigger SmokeConnect Chrome extension or Smoke Keychain for log in, if user don't have Chrome extension enabled it will fallback to SmokeConnect website.

```
var params = {};

// The "username" parameter is required prior to log in for "Smoke Keychain" users.
if (smokeconnect.useSmokeKeychain) {
  params = { username: 'fabien' };
}

client.login(params, function(err, token) {
  console.log(err, token)
});
```

### Get login URL for OAuth 2
The following method returns a URL that you can redirect the user to so that they may log in to your app through SmokeConnect:
```
var link = client.getLoginURL(state);
// => https://smokeconnect.com/oauth2/authorize?client_id=[app]&redirect_uri=[callbackURL]&scope=vote,comment&state=[state]
```
Parameters:
- __state__: Data that will be passed to the callbackURL for your app after the user has logged in.

After logging in, SmokeConnect will redirect the user to the "redirect_uri" specified in the login url above and add the following query string parameters for your app to use:
- __access_token__: This is the oauth2 access token that is required to make any Smoke API calls on behalf of the current user. Once you have this you need to tell the SmokeConnect.js to use it by either specifying it as a parameter to the init() method call or by calling sc2.setAccessToken([accessToken]).
- __expires_in__: The number of seconds until the access token expires.
- __username__: The username of the current user.

### Get user profile
Once a user is logged in to your app you can call the following method to get the details of their account:
```
client.me(function (err, res) {
  console.log(err, res)
});
```
If it is successful, the result will be a JSON object with the following properties:
```
{
  account: { id: 6329, name: "herbncrypto", ...},
  name: "herbncrypto",
  scope: ["vote"],
  user: "herbncrypto",
  _id: "herbncrypto"
}
```

### Logout
The revokeToken() method will log the current user out of your application by revoking the access token provided to your app for that user: 
```
client.revokeToken(function (err, res) {
  console.log(err, res)
});
```

### Vote
The vote() method will cast a vote on the specified post or comment from the current user:
```
client.vote(voter, author, permlink, weight, function (err, res) {
  console.log(err, res)
});
```
Parameters:
- __voter__: The Steem username of the current user.
- __author__: The Steem username of the author of the post or comment.
- __permlink__: The link to the post or comment on which to vote. This is the portion of the URL after the last "/". For example the "permlink" for this post: https://smoke.io/life/@herbncrypto/the-highest-point-of-my-week would be "the-highest-point-of-my-week".
- __weight__: The weight of the vote. 10000 equale a 100% vote.
- __callback__: A function that is called once the vote is submitted and included in a block. If successful the "res" variable will be a JSON object containing the details of the block and the vote operation.

### Comment
The comment() method will post a comment on an existing post or comment from the current user:
```
client.comment(parentAuthor, parentPermlink, author, permlink, title, body, jsonMetadata, function (err, res) {
  console.log(err, res)
});
```
The comment() method is rate limited to 5 minutes per root comment (post), and 20 seconds per non-root comment (reply).

### Delete comment
The deleteComment() method will mark a comment as deleted.
```
client.deleteComment(author, permlink, function (err, res) {
  console.log(err, res)
})
```

### Custom json
```
client.customJson(requiredAuths, requiredPostingAuths, id, json, function (err, res) {
  console.log(err, res)
});
```

### Reblog
```
client.reblog(account, author, permlink, function (err, res) {
  console.log(err, res)
});
```

### Follow
```
client.follow(follower, following, function (err, res) {
  console.log(err, res)
});
```

### Unfollow
```
client.unfollow(unfollower, unfollowing, function (err, res) {
  console.log(err, res)
});
```

### Ignore
```
client.ignore(follower, following, function (err, res) {
  console.log(err, res)
});
```

### Claim reward balance
```
client.claimRewardBalance(account, rewardSmoke, rewardVests, function (err, res) {
  console.log(err, res)
});
```

## Send operation

### Transfer 

```
const op = ['transfer', {
  from: '__signer',
  to: 'herbncrypto',
  amount: '0.001 SMOKE'
}];
steemconnect.sendOperation(op, {}, function(err, result) {
  console.log(err, result);
});
```
