---
layout: post
title:  "Day of REST Notes"
date:   2017-03-09 10:34:51 -0500
categories: tools wordpress api
vertical: Code
excerpt: "Day of REST #feelingRESTful"
---

Add links to your response `_links` `link type`: `/wp/v2/etc...` which can then be added to the API base to get more data through that link.

If embeddable then you can add `?_embed` to the end.

You can use `enum` on something like status to list the potential things that a schema provides. (See post status.)

Different Authentication methods are available and new ones are plugable. The basic support is cookie based, basic auth, oauth 1 and brokered auth.

`wp.api` is automatically loaded inside wordpress for using the API. It uses cookie authentication out of the box.

Basic Auth is the type your browser uses, not very secure, browser pops up and data is sent in plain text.

When connecting from one site to another, OAuth is the recommended approach.

OAuth 2 is a little more limited in scope b/c it requires HTTPS.

== OAuth 1
 - Install the REST API OAuth 1.0 plugin
 - Create a new app in the WordPress Dashboard.
 - Continue with the OAuth 1.0 flow
 - oauth1.wp-api.org.

The steps for a user are quite arduous, they have to create an app and authorize it.

The solution for this difficult project is Brokered Authentication.

== Brokered Authentication
 - Register at apps.wp-api.org
 - The App that you are building, the WordPress site you want to use, the apps broker.
 - The app contacts the broker.
 - The broker contacts the site
 - The broker than forwards the key to the app.
 - apps.wp-api.org/spec
 - apps.wp-api.org/app-developers

 Client Libraries are a major part of the process.
  - Backbone Core Client - `wp_enqueue_script('wp-api')`
   - gives you the `wp.api` object for access.
  - `npm install wordpress-rest-api-oauth-1`
  - wpapi NodeJS/Browser - for use in a client or Browser
   - `npm install wpapi`

== Developing
 - `register_meta`
 - `register_settings`
 - `register_rest_field`
 - You can use the WP_REST_Controller to create totally new methods.

= API Tales of Woe and Woah

If you use GET for destructive actions then suddenly Google's crawler can start
deleting stuff. Use PATCH/DELETE or at least POST for destructive actions.

Don't mess with the internet's conventions.

Don't do Slow Stuff - don't make the user wait for slow stuff to happen, async as much as possible.

But also check to make sure your async code is actually quicker.

Defensive routing
 - Support trailing slashes but only as looking for something you're meant to be looking for.
 - So `/foo/` maybe should deliver a 404 but `/foo` should deliver the first page of foos.
 - Be error heavy
 - Apply limits.
 - Apply pagination.
 - Apply pagination limits, an upper and lower bound (don't let people request -1 page)

Includes are cool, but restrict what people can include.

Easy win - use SSL - now with letsencrypt.org it is free.

Universal Uniqueness
 - Avoid people downloading your entire dataset and putting you out of business.
 - UUIDs stop slow and malicious downloading of your public but unique data.
 - Don't let people illegally download all your data, or at least detect it.
 - UUID performance tweaks exist.

Runscope useful for testing APIs.

Errors should throw useful and HTTP error codes. Don't have your error pages 200.
 - This is useful for tracking systems like Runscope or New Relic.

REST has nothing to do with returning status codes properly, you should always be doing that.

Return things that make sense to both a computer and a human.

Put a link to the documentation in your error!

Retry failures from the client side.
 - If a request gets a server timeout, try it again.
 - Maybe try it twice, but wait a second or two
 - Check the status code, anything 500 error can usually be retried.

`hello.js` is a package for logging into an OAuth.

429 usually means rate limit exceeded, except Google sends a 403.

"Google is not really helping out when they ignore the conventions"

HTTP Status Cats.

Don't retry on all failures. If you go over the rate limiting pop a 429 error that maybe has a Retry-After header to tell you when you should be retrying.

It is more important to give useful feedback to your clients.

Don't cache errors! (Don't let Varnish cache your 429 errors)

By default 404 gets cached on Varnish, which can cause trouble for things like API requests.
 - For 10 minutes a thing might not exist in your app at all, with the default Varnish configuration.
 - If the status code is X set the time to live (ttl) lower, like to 0 or your Retry-After?

 Expect the worst from everyone.

If it is not JSON - catch that exception!

Also catch HTTP server errors when you can!

The response might be empty if it is a timeout. Check for that.

Never assume the shape of the data, check for it and throw an error when it returns an unexpected shape.

Documentation is important - Swagger, Ramble(sp?), etc... JSON Schema

Stop services from lying to each other
 - [Strong Parameters](packagist.org/packages/koine/strong-parameters)
 - PHP-VCR instead of static stubs
 - Be sure to re-record VCR stubs
 - Jenkins can host entire stack with Docker Compose.
 - Set up with Travis as well is possible.

Build APIs You Won't Hate - apisyouwonthate.com
 - `wordpress` coupon code.

If you start to find that your REST API becomes a query language, mby time to add a GraphQL endpoint.

JSON Schema can dictate the shape of the GraphQL data as well.

Some people can use GraphQL to stitch multiple requests together.

= Backbone

@roundearth

 - Underscore.js and Backbone.js

== Backbone
 - Events, Routes, History
 - Makes it easy to push new URLs into the history of the browser.
  - History contains states.
 - Sync - fetch and save - save a model to server and can create a new resource.
 - Admin features, repeatable meta, live status page, interactive features, extend core, data display and manipulation.
 - the `wp-api` script will build out the map from your REST schema and match it.
 - `post.fetch()` will bring the REST API data into a JS object.
 - `post.fetch` can specify fields or that you want it to be embeddable. Add `.done` to the end to handle the object when it is ready.
 - `post.set(field, value)` uses cookie authentication.
 - Also uses nonce.
 - `wp.api.models` - create `new` - will return a complete post object. Other objects can be created (`Posts()` or `Books()`)
 - Works for custom namespaces, post types, remote WordPresses.
 - `wp.api.collections`
 - backbone views use templates that are just HTML with datapoints in them `{{object}}`
 - Various helpers on `getX`
 - `Create React App`
 - tunedin.net
 - Us Too website (sp?)

 Backbone - sync and templates, plus listener system. Includes underscore (or use lodash).

== React
 - ryelle.codes/basic-react-example
 - JSX is strict
 - Webpack yo!
 - Babel
 - redux.js.org - egghead.io
 - React Router
 - create-react-app
 - Who's using react?
  - Jetpack
  - Foxhound theme
  - Wordcamp US Social Stream
  - usTwo.com - ustwocom architecture - node app is website, wordpress API powers it.
 - Think about how people are going to use your app
 - Accessibility
  - Screen Readers have javascript enabled
  - webaim.org
  - You need to tell the screen reader that the page is changing when you use JS.
 - Server side rendering - isomorphic
 - ryelle.codes/calypso-extensions

@ryelle

= Rest projects
 - WAMU
 - Unified content architecture and editorial / disperate presentation
 - Allows you to create multi-headed presentations.
 - WordPress can be one microservice among others and use it as an infrastructure element.
 - Load more / infinite scroll
 - filtered / faceted search results
 - related stories
 - Sponsored content / native ads
 - Site networks - cross site content
 - Syndication push or pull across sites in a networks
 - pull based on taxonomy from post type endpoint
 - Admin ui to Push article to a site I am viewing to which I have permissions.
 - Improved author experiance.
 - Creating complex content types.
 - Infrequent user forms and guided wizards (like foundation)
 - Improved editor experiance
 - Integration with other services.
 - Integration with desktop apps
 - Integration with mobile apps.
 - @jeckman

= Design Perspective on REST API
 - Better product Design
 - More tailered interfaces
 - dribble.com/shots/3344228-Moving-task-flow-interactions
 - If users don't see a change taking place they don't know a change occurs
 - Disney's 10 properties of animation

= Prep non devs for REST API
 - feelingrestful.com
 - react-motion
 - Don't assume. Talk and Document.
 - Be kind to the people you work with, don't throw them in the ocean to teach them to swim.
 - hmn.md/whitepaper

= Real Time REST Apis.
 - Right now you have to stalk your endpoints.
 - Sometimes you need a little push
  - Updating headless front-end application views for content changes.
  - Notifying a client or micro service application about a content event.
  - Attempting to keep things in sync between WP and another application, users newsletters, etc...
  - Breaking cache or resetting cache headers.
 - Webhooks + WordPress Actions!
  - webhooks.pbworks.com/w/page/13385124/FrontPage
  - an HTTP callback, an HTTP POST that occurs when something happens, a simple event-notification via HTTP host.
  - WordPress hooking actions `do_action` style.
  - `notify_user_registered` - Add action to an event that does a `wp_safe_remote_post` to mock an internal rest API request and send it.
 - [Rest Hooks - not a specification - patterns](http://resthooks.org/docs/)
 - Mechanism to store subscriptions
 - Mechanism to list, create, modify, delete, subscriptions
 - list event hooks via API endpoints
 - Mechanism to send
 - hooks should be noun.verb
 - /hooks endpoint.
 - `id`, `hook name`, `application`, `uri`, `active`
 - When the condition on the hook is realized transmit the response data to all subscribers.
 - Error handling and optionally retrying connnections for 4xx/5xx errors
 - RPC API from a REST API.
 - http://speakerdeck.com/rachelbaker/real-time-rest-api-with-wordpress

= API Client API Design
 - Understanding how a tool work lets you use it better.
 - talks.kadamwhite.com/ador-boston.
 - Interface loyalty is a drive in decision making for consumers.
 - `npm install wpapi`
 - `new WPAPI` works in node and the browser
  - Creates a site client instance.
  - `wpapi`
  	1. Load a JSON object of the wp-json response.
  	2. Take the hierarchy and turn it into a route tree.
	3. Create Route Handler factories `WPRequest`
	 - Create a setter function
	 - `parameterMixins.before` - to get items via parameters
	 - will construct your query string.
	4. Separate Handlers by Namespace
	 - add `wp/v2` to the base `site` WPAPI instance.
	 - Autodiscovery
 - Models vs collections - seperate it out.
 - "The users of our code are not going to be the same people we are now when we are writing it."
 - WP API Testbed - wp-notebook
 - http://talks.kadamwhite.com/ador-boston
 - http://github.com/kadamwhite/wp-notebook

= HTTP kinda sucks sometimes
 - javascript adventure club
 - @benjaminbenben
 - github.com/benfoxall
 - Network of things!
 - Small devices, sketchy netwoks, low processing power
 - MQTT - Message Queue Telemetry Transport
 - Many connection types.
 - 14 bytes to handshake
 - QoS - when you are not sure your satalite is even there.
 - emqtt.io
 - AWS has iot tools
 - Puck.js
 - Espruino
 - ESP8266
 - Pi Zero 2
 - Try and work out what your thing does
 - Someone needs to understand the things you are creatng.
 - "Because it had this concept of thingness the mechanisms around it could move forward"
 - Keep it simple and stupid.
 - Skunk Works by Nickholas Means (sp?)
 - Browsers can do more than just displaying a page.
 - How can you build things that make a difference?
 - map.flood.network
 - A site can have a physical impact without being internet of things based.
 - "When you're building a thing make a difference for someone"

[party stats at 6pm]

post status club discount adayofrestboston
