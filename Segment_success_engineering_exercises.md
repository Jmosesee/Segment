+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Exercise #1: Example customer questions
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Description: Below are 3 common customer questions we receive. Please draft responses and/or followup responses to each question in the space below. This exercise helps us evaluate your ability to interpret customer requests and provide clear and comprehensive responses leveraging the available resources found in our docs, public site and app. Feel free to ask clarifying questions back to the customer in your response when appropriate. 

=======================================================================================
Question #1:
=======================================================================================
Hi Segment,

I'm using Wistia and I want to track my videos, specifically I want to track "Play", "Pause", and "End" events. 

- The events need to show up in Google Analytics as events.
- The GA event Category should be "Video" for all video events.
- The event label should be the name of the video being played. 

Can you help me with that? I'd love to see actual code samples on how to fire the event. 

Best,
Sara

Answer #1: 
================================================================

Dear Sara,

Thanks for your question, yes, I can help you with that!
To respond to the, "Play," "Pause," and "End," events of the Wistia video player, you'll need to use the [Wistia JS bind function](https://wistia.com/support/developers/player-api#bind-eventtype-callbackfn).  Please see a code sample below.  In this example, the string, 'abcde12345' is placeholder for an identifier, or "matcher," for your video.  Note that it matches a portion of the video's container class ID.  Is this example clear and does it accomplish what you wanted?  If not, or if I can offer any further assistance, please let me know.

Best,

Josh Moses

```
  <script src="//fast.wistia.com/assets/external/E-v1.js" async></script>
  <div class="wistia_embed wistia_async_abcde12345" style="width:640px;height:360px;">
  <!-- Insert your video here -->
  </div>
  <script>
    window._wq = window._wq || [];
    // Get a video handle
    _wq.push({ id: 'abcde12345', onReady: function(video) {
      // Run a callback function when a specific event is triggered
      video.bind("play", function() {
        // Send this event to Segment for tracking
        // The first argument here is the name of the event.
        // Segment will also pass the dictionary of properties on to Google Analytics
        analytics.track("play", {category: "Video", label: "Name of Video"});
      });
      video.bind("pause", function() {
        analytics.track("pause", {category: "Video", label: "Name of Video"});
      });
      video.bind("end", function() {
        analytics.track("end", {category: "Video", label: "Name of Video"});
      });
    }});
  </script>
```

=======================================================================================
Question #2:
=======================================================================================
Hi Segment,

We recently got up and running on Segment. Got the JS and PHP sources set up broadcasting to Mixpanel, GA and Intercom. I was wondering if it is also possible to connect Intercom as a cloud source and also broadcast the events back to other integrations.

So for example, it would be nice to we could track opens and clicks from Intercom with Segment so we can broadcast those to Mixpanel and GA. We can of course do this ourselves by creating a simple webhook and use the PHP source to do that. But was just wondering if there is an even easier way.

Best,
Jerome

Answer to Question #2: 
================================================================

Dear Jerome,

Hi, thanks for your message and welcome to Segment!  I hope everything is going smoothly for you so far.  To answer your question, yes you can connect Intercom as a cloud source!  The instructions for that are [here](https://segment.com/docs/sources/cloud-apps/intercom/).  Please start there but if you run into any difficulty, let me know, I'll be here.

Yours,

Josh Moses


=======================================================================================
Question #3:
=======================================================================================
Hey Segment,

I'm using analytics.js to track events and I’m currently sending data to Intercom and Google Analytics. I’m tracking a few events that I don’t want to send to Google Analytics, but I do want to send them to Intercom. Can I do this in the Segment UI? If so, where do I do that? Is there another way to filter events in the `.track()` call itself? 

Best,
Riley


Answer to Question #3: 
================================================================

Dear Riley,

Hi, thanks for your questions!  In the `.track()` call, yes, you can specify which integrations you want to send the events to.  See below for an example, and read [here](https://segment.com/docs/sources/website/analytics.js/#selecting-destinations) for further details:

```
analytics.track('Event Name',
  integrations: {
      'Google Analytics': false,
      'Intercom': true
  }
});
```
For [Business Tier members](https://segment.com/pricing/), there is an option directly in the Segment UI, on the source schema page, for your team to make changes to these filter selections.

Happy to help if you want to know more,

Josh Moses

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Exercise #2: Basic SQL exercise
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Description: Included below are credentials to access an example data set in a Postgres data warehouse, along with some questions requiring basic mysql analysis to answer.


=======================================================================================
Postgres cluster credentials:
=======================================================================================
Hostname: ec2-54-83-194-117.compute-1.amazonaws.com
Password: AbVv-wMQGmbOwFpeSdw9paFipJ
Username: ujzapgrhcborxq
Port: 5432
Database: dbt6sotajkgv26
Schema: music_is_me


=======================================================================================
Question #1: How many users canceled their plan? List them by name here.
=======================================================================================

Answer #1: Only one: Kenny Rogers

```
SELECT "name"
	FROM users
	WHERE plan_name = 'Canceled';
```
or

```
SELECT "name"
 	FROM cancel_account
 	LEFT JOIN users
 	ON cancel_account.user_id = users.id;
```

=======================================================================================
Question #2: How many different plans can users sign up for? What are they?
=======================================================================================

Answer #2: Users have signed up for 3 plans: Basic, Advanced, and Enterprise.  Except, there does seem to be one user, "Guy Lombardo," signed up for a null plan.

```
SELECT DISTINCT plan_name
	FROM signed_up;

SELECT "name"
	FROM signed_up
	LEFT JOIN users
	ON signed_up.user_id = users.id
	WHERE signed_up.plan_name IS NULL;
```
=======================================================================================
Question #3: How many users are signed up for each plan? Please present the result in a single table ordered by the number of users in each plan.
=======================================================================================

Answer #3:

| Plan Name  | Count |
|------------|-------|
| Basic      |    19 |
| Advanced   |    27 |
| Enterprise |    31 |

```
SELECT plan_name, count(id)
	FROM users
	WHERE plan_name IS NOT NULL and plan_name != 'Canceled'
	GROUP BY plan_name
	ORDER BY count(id);
```
=======================================================================================
Question #4: A few users have appear to have a null `plan_id`. The original events all included a value for the `plan_id` but some were sent through as an integer (`plan_id: 1`) and others were sent as a string (`plan_id: '1'`). Why would that matter? 
=======================================================================================

The data type of the `plan_id` field of the users table is TEXT.  All data sent into this field must be a string, enclosed in single quotes, or converted to a string by using CAST ( expression AS TEXT )


=======================================================================================
Extra Credit: Are there any other insights you were able to uncover when analyzing the data set?
=======================================================================================

Benny Goodman tried to upgrade to the Advanced plan, but this upgrade is not reflected in his account.  Why not?  He or she is the only user with such a discrepancy.
```
-- Check to make sure the plan indicated in the user's account matches the plan they signed up for, or upgraded to, unless they cancelled.
SELECT users.id, users.name, signed_up.plan_name, signed_up.timestamp, upgrade_plan.plan_name, upgrade_plan.timestamp, users.plan_name
	FROM users FULL OUTER JOIN signed_up
	ON users.id = signed_up.user_id
-- 	Include only the latest signup
	INNER JOIN(
		SELECT user_id, MAX(timestamp) as timestamp
		FROM signed_up
		GROUP BY user_id
	) as last_signup
	ON signed_up.user_id = last_signup.user_id AND signed_up.timestamp = last_signup.timestamp
	LEFT JOIN upgrade_plan
	ON users.id = upgrade_plan.user_id
	LEFT JOIN cancel_account
	ON users.id = cancel_account.user_id
-- Find mismatches in plans that haven't been cancelled
-- If the plan was upgraded, the upgrade plan doesn't match the current plan
-- Or, if the plan wasn't upgraded, and the last signup doesn't match the current plan
	WHERE cancel_account.user_id IS NULL
		AND ((upgrade_plan.plan_name IS NOT NULL
				AND users.plan_name != upgrade_plan.plan_name)
			OR (upgrade_plan.plan_name IS NULL
				AND users.plan_name != signed_up.plan_name))
	ORDER BY signed_up.user_id;
```
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Exercise #3: Explaining a technical concept
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Description: For this exercise, we'd like to test your ability to read code and provide clear technical and non-technical descriptions of what the code is solving for.

=======================================================================================
Question #1: Describe what the following code block does when executed in a browser
=======================================================================================
Answer #1: This function returns a JavaScript object, populated with the query parameters from the current URL.  Each field name and value from the URL query string becomes a property name and value in the JavaScript object.  Any upper-case letters in the field names of the URL query string are converted to lower-case letters in the property names of the JavaScript object.  The values are not modified.

```
function formatQs() {
	var output = {};
	var qs = document.location.search.substring(1);
	qs = qs.split('&');

	for (var i = 0; i < qs.length; i++) {
		var tokens = qs[i].split('=');
	  output[tokens[0].toLowerCase()] = tokens[1];
	}

	return output;
}
```

=======================================================================================
Question #2: Describe a specific scenario in which the above function would be used for tracking purposes, and more specifically how Google Analytics uses the data to track marketing campaigns.
=======================================================================================
A URL, or web address, can include an optional component called the, "query string."  This string allows the web browser to send data to the web server.  Companies can track marketing campaigns by adding meaningful data to the query string of URL's, or links, published for the campaign.  For example, a company can publish a digital advertisement that invites the audience to click on a link to visit the company's web page.  The query string of the link offered in the ad can incorporate several details about the advertisement, such as the name of the website on which it was placed.  When someone sees the advertisement and clicks on the link, those query string details are sent to Google Analytics for tracking.  The reports generated by Google Analytics from this collected data allow the company to analyze the effectiveness of their marketing campaigns.  The function above extracts the data from the query string.


=======================================================================================
Question #3: Assuming that the analytics.js library is loaded on the page, please update the code block above to send through the standard utm parameters as properties in an analytics.track() call. Additional requirements:
* Only send the officially supported utm campaign parameters as properties
* Only send utm parameter properties that are included in the original URL query string (see below for example)
* The track call event name should be "User Referred"
* Example url: https://example.com/home?utm_source=google&utm_campaign=simulator_push&utm_content=trees&utm_name=forest
=======================================================================================

Answer #3:

```
// The formatQs() function is good as it is--it doesn't need to be changed for this assignment
function formatQs() {
	var output = {};
	var qs = document.location.search.substring(1);
	qs = qs.split('&');

	for (var i = 0; i < qs.length; i++) {
		var tokens = qs[i].split('=');
	  output[tokens[0].toLowerCase()] = tokens[1];
	}

	return output;
}

function trackUTMThroughSegment() {
    // These are the officially supported utm campaign parameters
    var supportedParams = ['utm_source', 'utm_medium', 'utm_campaign', 'utm_term', 'utm_content'];
    var urlParams = formatQs();
    var filteredParams = {};
    supportedParams.forEach(function(element) {
        if (urlParams.hasOwnProperty(element)) {
            filteredParams[element] = urlParams[element];
        }
    });

    /* Although the instructions specify, "Only send the officially supported utm campaign parameters as properties" it might be beneficial to make an exception for the special case of, 'utm_name', which is not officially supported but could be mistakenly substituted for 'utm_campaign.'  To implement this proposed exception, uncomment the following:

    if (!urlParams.hasOwnProperty['utm_campaign'] && urlParams.hasOwnProperty['utm_name']) {
        filteredParams['utm_campaign'] = urlParams['utm_name']
    }
    */

    analytics.track("User Referred", filtered);
}
```

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
End
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
