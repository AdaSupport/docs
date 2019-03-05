# Meta Variable Deletion - Developer Docs

> Meta Variables are automatically collected and specific to an individual chatter, as part of the implementation of your **Ada** bot. Any information passed from **your databse** to the bot as metadata will be stored by Ada as a Meta Variable of that chatter. However, Ada provides two API endpoints which you can use to ensure that given Meta Variable values are deleted from a given chatter.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## Prerequisites
This document is intended *for developers* with working knowledge of REST APIs and JavaScript. It is assumed that you have write-access to your company's code repository which contains **Ada Embed**.

The following instructions will reference the usage of a **chatter token**. See the [Ada Embed docs](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) for more information how to get back a chatter token using ```chatterTokenCallback```.

## Deleting Meta Variable values in reaction to an event
For security reasons, you might want to delete the values stored in a specific Meta Variable for a specific chatter, typically in reaction to an event such as a chatter being **properly closed.**

As long as you have a **chatter token** on hand, you can delete the values for one or more Meta Variables stored by Ada for that chatter instance.
| NOTE: This deletion method is only effective in situations where a chatter is properly closed (ie. on logout). For situations where a chatter is improperly closed (ie. on browser crash, on tab-close, on window-close), please see "Deleting Meta Variable values at a specific time". |
| --- |

##### PATCH `https://<bothandle>.ada.support/chatters/<chatter-token>/remove_from_storage`
#
##### Expected Keys
This request body of this endpoint expects the following:
Parameter | Description | Optionality
--- | --- | ---
`variables` | An array that takes one or more Meta Variable names. | **needed**
##### Example Request Body
Here is an example of what you can pass to the endpoint:
```
{"variables":
	["variable_1", "variable_2"]
}
```

## Deleting Meta Variable values at a specific time
When chatters are improperly closed (eg. on browser crash, on tab-close, on window-close), Meta Variable values cannot be immediately deleted. In such instances, the deletion task will fall to a background job run by Ada every 24 hours.

Ada's variable-deletion backgroud job runs on a nightly basis, searching for all chatter instances with "expired" Meta Variables. A variable is considered expired if the date and time has already passed in relation to the date and time at which Ada's background job is run. The job will delete the values of these expired Meta Variables and remove the "expiry settings" from a given chatter.

##### PATCH `https://<bothandle>.ada.support/chatters/<chatter-token>/set_expiry`
#
##### Expected Keys
This request body of this endpoint expects the following:
Parameter | Description | Optionality
--- | --- | ---
`variable_expiry_settings` | An object that has two properties, `expiry` and `meta_vars`. | **needed**
`expiry` | An ISO 8601 formatted datetime (extended notation), with an optional timezone. | **needed**
`meta_vars` | An array of Meta Variable names. | **needed**

##### Request body
The body of the request looks like this:
```
{"variable_expiry_settings":
	{
		"expiry": "2011-10-05T14:48:00.000Z",
		"meta_vars": ["variable_1", "variable_2"]
	}
}
```


## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).