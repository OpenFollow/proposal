# Open Follow Proposal

This is a proposal for a common service that would facilitate follows, likes, and boosts across Fediverse servers.  Strict care is given to end-user privacy and security.  Improvements welcome.

## About

As of June 2023, one of the core User Experience (UX) concerns raised by Fediverse developers is the difficulty of discovering and following across instances.  If a user (Alice) opens a link on a site that is not her own and discovers an interesting user (Bob) who she would like to follow, or a post of Bob's that she would like to boost, the subsequent workflow required of her is not ideal.  Alice must 1) find and copy Bob's profile URL (or the specific post of his) then 2) switch apps or websites to return to her home server or mobile client, then 3) search for the URL she was just on in her own server, then 4) finally follow, like, or boost the content she wanted.  This is especially difficult on mobile devices.

In a centralized system, this is a single-click process that doesn't require any thought.  On Twitter, Facebook, LinkedIn, and others, each page has a "Follow" or "Like" link that users can click and the job is done.

## Technical Details

This same workflow could be accomplished in a distributed system using a common service that helps users pull in their home server credentials when desired.  And, it can be done in a secure, and privacy-preserving way.  

### High-Level Outline

1. A common server (named OpenFollow.org for this example) hosts static pages with no cookies, tracking, or logging of any kind.  These static pages would probably contain mostly Javascript, with a few small images as necessary.

2. OpenFollow.org would publish a series of Javascript widgets that are embedded into various Fediverse apps.  These widgets could be embedded into a web page with a script tag as simple as 
```html
<button data-open-follow="signin"/>
<script src="https://openfollow.org/widget/signin"></script>
```

3. When Alice signs in to a supporting app, the login confirmation page runs the "sign-in" intent from the OpenFollow.org website.  This widget stores her Profile URL and some other key metadata in the browser's `localStorage` datastore.  This information is never transmitted to the static OpenFollow.org server.  More importantly, because this data is stored by a script on the openfollow.org site, it is not available to Bob's home server.  

4. Later, when Alice visit's Bob's profile or posts, Bob's server also embeds OpenFollow.org widgets for "follow" and "like" intents.  These display buttons that are possibly customizable, but easily recognizable to Alice.  

5. When Alice clicks on a "follow", "like", or "boost" button, the scripts hosted by OpenFollow.org ARE able to retrieve Alice's stored credentials and metadata.  This information is used to redirect Alice to a confirmation page on her home server that will be pre-populated with Bob's profile URL (or page URL, or whatever the intent requires).  Alice can confirm her follow or like, and the process is complete.

### Forthcoming Browser APIs

There is an experimental browser API called [FederatedCredentials](https://caniuse.com/mdn-api_federatedcredential) that has the potential to address this use case at the browser level.  This would be a very good thing.

Unfortunately, FederatedCredentials is not supported outside of Chrome and Edge, so it cannot be depended upon as a global solution.  

However, OpenFollow.org could be used as a sort of polyfill that eases access  into this API if it is available, and falls back on its own localStorage mechanism otherwise.

## Some Details

* OpenFollow should support multiple identities, so that a user can be signed in to multiple services on a single browser.  In this case, the Follow/Like/Boost buttons should give the user a choice about which service to forward to.

* Users should also have the ability to "sign out" and remove OpenFollow metadata from their browser.

## Potential Pitfalls

* A malicious website might "fake" an OpenFollow signin, leaving forwarding information in their browser for later.  If the user later likes a piece of content, they could be redirected to a malicious address.  How best to prevent malicious sites from leaving "spam" in the OpenFollow directory.
