# Meta Variable Deletion - Developer Docs

> Meta Variables are automatically collected and specific to an individual chatter, as part of the implementation of your **Ada** bot. Any information passed from **your database** to the bot as metadata will be stored by Ada as a Meta Variable of that chatter. However, Ada provides two API endpoints which you can use to ensure that given Meta Variables are deleted from a given chatter.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## Prerequisites
This document is intended *for developers* with working knowledge of REST APIs and JavaScript. It is assumed that you have write-access to your company's code repository which contains **Ada Embed**.

The following instructions will reference the usage of a **chatter token**. See the [Ada Embed docs](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) for more information on how to get a chatter's token using ```chatterTokenCallback```.

## PATCH `/chatters/<chatter-token>/remove_from_storage`
You must have a chatter token to use this endpoint: `https://<bothandle>.ada.support/chatters/<chatter-token>/remove_from_storage`.

This endpoint allows you to delete a chatter's Meta Variables. It must be used in reaction to an event, such as a chatter being **properly closed** (eg. on user logout).

Meta Variable deletion for chatters that are **improperly closed** (eg. on tab-close, on window-close, on browser crash) is handled by PATCH `https://<bothandle>.ada.support/chatters/<chatter-token>/set_expiry`. This endpoint must be used to set and remove Meta Variable expiry settings on all chatters (see docs below).

### Expected Keys

Parameter | Description | Optionality
--- | --- | ---
`variables` | An array. Takes one or more Meta Variable names. Will **not** throw an error if the array is empty. | **required**
### Example Request Body
```
{"variables":
	["variable_1", "variable_2"]
}
```
#### Example Response: 200
```
{"message" : "Cleared meta variables from chatter storage"}
```

## PATCH `/chatters/<chatter-token>/set_expiry`
When chatters are improperly closed (eg. on browser crash, on tab-close, on window-close), their Meta Variables cannot be immediately deleted. In such instances, the deletion task will fall to a background job run by Ada every 24 hours.

Ada's variable-deletion background job runs on a nightly basis, searching for all chatter instances with "expired" Meta Variables. The job will delete these Meta Variables and remove the "expiry settings" from a given chatter.

This endpoint therefore serves two purposes, as it must be used to:
1. Set `variable_expiry_settings` on all chatters, which allows an **improperly closed** chatter to be picked up by Ada's nightly background task so that its expired Meta Variables can be deleted;
1. Remove `variable_expiry_settings` from a given chatter whose Meta Variables are deleted using PATCH `https://<bothandle>.ada.support/chatters/<chatter-token>/remove_from_storage`, so that the chatter is **not** picked up by Ada's nightly background task.

### Expected Keys
Parameter | Description | Optionality
--- | --- | ---
`variable_expiry_settings` | An object. Its value can be another object with keys `expiry` **AND** `meta_vars`. <br> Its value can also be `null`. | **required**
`expiry` | An ISO 8601 formatted datetime (extended notation) UTC timestamp. | **optional**
`meta_vars` | An array of Meta Variable names. | **optional**

### Example Request Body: Setting `variable_expiry_settings`
```
{"variable_expiry_settings":
	{
		"expiry": "2011-10-05T14:48:00",
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