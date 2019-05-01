# Meta Variable Deletion - Developer Docs

> Meta Variables are automatically collected and specific to an individual chatter, as part of the implementation of your **Ada** bot. Any information passed from **your database** to the bot as metadata will be stored by Ada as a Meta Variable of that chatter. However, Ada provides two API endpoints and runs a nightly task, all of which work together to ensure that given Meta Variables are deleted from a given chatter.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## **Example Code**
[See example code at JSFiddle](https://jsfiddle.net/asyi4/o62dt0sw/)

## **Prerequisites**
This document is intended *for developers* with working knowledge of REST APIs and JavaScript. It is assumed that you have write-access to your company's code repository which contains **Ada Embed**.

The following instructions will reference the usage of a **chatter token**. See the [Ada EmbedScript docs](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) for more information on how to get a chatter's token using ```chatterTokenCallback```.

The following instructions also abide by the assumption that any meta variable you have previously passed to Ada via EmbedScript - whether by [query paramaters](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#faq), the [setMetaFields](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#setmetafieldsmetafields-param-object) action or the [metaFields](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#metafields-type-object) settings object - only consist of sanitized names.

Because unsanitized meta variable names are sanitized by Ada's backend, searching for - and removing - unsanitized variables using the endpoints described below will not be possible (and may result in unexpected issues).

Name | Description | Will be sanitized | Sanitized version
--- | --- | --- | ---
`phone` | Does not contain capitalized letters, special characters, whitespace, underscores or periods. **Recommended format**. | **No** | `phone`
`phone_number` | Contains an underscore. Does not contain capitalized letters, special characters, whitespace or periods. **Recommended format**. | **No** | `phone_number`
`phone number` | Contains whitespace. Does not contain capitalized letters, special characters, periods or underscores. **Not recommended.** | **Yes** | `phone_number`
`PHONE NUMBER` | Contains whitespace and capitalized letters. Does not contain special characters or periods. **Not recommended.** | **Yes** | `phone_number`
`Phone` | Contains capitalized letters. Does not contain whitespace, special characters or periods. **Not recommended.** | **Yes** | `phone`
`phone.number` | Contains a period. Does not contain capitalized letters, special characters, whitespace or periods. **Not recommended.** | **Yes** | `phone_number`
`phone _number` | Contains whitespace and an underscore. Does not contain capitalized letters, special characters or periods. **Not recommended.** | **Yes** | `phone__number`
`phone😡number` | Contains an emoji. Does not contain capitalized letters, special characters, whitespace, periods or underscores. **Not recommended**. | **No**, but will cause other issues. | `phone😡number`
`phone!number` | Contains a special character. Does not contain capitalized letters, whitespace, periods or underscores. **Not recommended.** | **No** | `phone!number`
`phone%20number` (passed in via [queryParams](#FAQ)) | Contains URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **Yes**| `phone_number`
`phone%20number` (passed in via [setMetaFields](#setmetafieldsmetafields-param-object) action) | Contains what looks like URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **No**| `phone%20number`
`phone%20number` (passed in via [metaFields](#metafields-type-object) settings object) | Contains what looks like URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **No**| `phone%20number`

## **Overview**
Deleting Meta Variables takes a few different steps ([see example code here](https://jsfiddle.net/asyi4/o62dt0sw/)). Here is a high-level overview of what you will need to do.

1. Set Meta Variable "expiry settings" on all chatter instances by making a PATCH request to `/chatters/<chatter-token>/set_expiry`.

	This step is responsible for setting a list of Meta Variables that need to be deleted, in addition to setting an expiry time. (The expiry time is used by Ada's nightly deletion task to handle cases where Meta Variables are not properly deleted).

	See [Setting and removing variable_expiry_settings](#2-Setting-and-removing-variable-expiry-settings).

1. Delete any specified Meta Variables for a given chatter upon successful "session end" (eg. logout). To do this, make a PATCH request to `/chatters/<chatter-token>/remove_from_storage`.

	See [Deleting Meta Variables](#1-Deleting-Meta-Variables).

1. Remove Meta Variable "expiry settings" from said chatter, immediately after successfully deleting Meta Variables as directed in step 2. To do this, Make a PATCH request to `/chatters/<chatter-token>/set_expiry`.

	This step is to ensure that Meta Variables that have already been successfully deleted in step 2 are not caught again by Ada's nightly deletion task.

	See [Setting and removing variable_expiry_settings](#2-Setting-and-removing-variable-expiry-settings).



## **The Endpoints**

## 1. Deleting Meta Variables
You must have a [chatter token](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) to use this endpoint:
### PATCH `https://<bothandle>.ada.support/api/chatters/<chatter-token>/remove_from_storage`


This endpoint allows you to delete a chatter's Meta Variables. It must be used in reaction to an event, such as a chatter session being **properly ended** (eg. on user logout).


### Expected Keys

Parameter | Description | Optionality
--- | --- | ---
`variables` | An array. Takes one or more Meta Variable names. Will **not** throw an error if the array is empty or if variable names are incorrect/non-existent.  | **required**
### Example Request Body
```
{"variables":
	["variable_1", "variable_2"]
}
```
### Example Response: 200
```
{"message" : "Cleared meta variables from chatter storage"}
```
Ada's nightly deletion task works with the [set_expiry endpoint (see docs below)](#2-Setting-and-removing-variable-expiry-settings) to handle Meta Variable deletion for chatter sessions that have been **improperly ended** (eg. on tab-close, on window-close, on browser crash).

## 2. Setting and removing variable expiry settings

You must have a [chatter token](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) to use this endpoint:
### PATCH `https://<bothandle>.ada.support/api/chatters/<chatter-token>/set_expiry`

When chatter sessions have not been properly ended (eg. on browser crash, on tab-close, on window-close), their Meta Variables cannot be immediately deleted by the [deletion endpoint](#1-Deleting-Meta-Variables). In such cases, the deletion task will fall to a nightly background task by Ada every 24 hours.

Ada's nightly deletion task searches for all chatter instances with Meta Variable *expiry_settings*. The job will delete these Meta Variables and then remove the *expiry_settings* from these chatter instances.

This endpoint therefore serves two purposes, as it must be used to:
1. Set `variable_expiry_settings` on all chatters. This allows a chatter whose session was **improperly ended** to be picked up by Ada's nightly background task, so that its expired Meta Variables can be deleted.

1. Remove `variable_expiry_settings` from a given chatter whose Meta Variables are deleted using the [deletion endpoint](#1-Deleting-Meta-Variables). This is to ensure that a chatter whose Meta Variables have already been successfully deleted is **not** picked up by Ada's nightly background task. You  must do this **after** making a successful PATCH request to the [deletion endpoint](#1-Deleting-Meta-Variables).

### Expected Keys
Parameter | Description | Optionality
--- | --- | ---
`variable_expiry_settings` | An object. Its value can be another object with keys `expiry` **AND** `meta_vars`. <br> Its value can also be `null`. | **required**
`expiry` | An ISO 8601 formatted datetime (extended notation) UTC timestamp. Eg. *2011-10-05T14:48:00* | **optional**
`meta_vars` | An array of Meta Variable names. Will **not** throw an error if the array is empty or if variable names are incorrect/non-existent. | **optional**

### Example Request Body: Setting `variable_expiry_settings`
```
{"variable_expiry_settings":
	{
		"expiry": "2011-10-05T14:48:00",
		"meta_vars": ["variable_1", "variable_2"]
	}
}
```
Given that you will likely want to dynamically set the value of **expiry** in the request body, it may be useful to do something along the lines of:
```
# Get time 24 hours from current time
const expiryTime = new Date(new Date().getTime() + (24 * 60 * 60 * 1000)
).toISOString();
```
and then pass *expiryTime* as the value of **expiry** in the request body like so:
```
{"variable_expiry_settings":
	{
		"expiry": expiryTime,
		"meta_vars": ["variable_1", "variable_2"]
	}
}
```


### Example Request Body: Removing `variable_expiry_settings`
```
{"variable_expiry_settings": null}
```
### Example Response: 200
This response applies to both setting and removing `variable_expiry_settings`.
```
{"message" : "Chatter meta variable expiry updated"}
```

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).