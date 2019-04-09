# Meta Variable Deletion - Developer Docs

> Meta Variables are automatically collected and specific to an individual chatter, as part of the implementation of your **Ada** bot. Any information passed from **your database** to the bot as metadata will be stored by Ada as a Meta Variable of that chatter. However, Ada provides two API endpoints which you can use to ensure that given Meta Variables are deleted from a given chatter.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## **Prerequisites**
This document is intended *for developers* with working knowledge of REST APIs and JavaScript. It is assumed that you have write-access to your company's code repository which contains **Ada Embed**.

The following instructions will reference the usage of a **chatter token**. See the [Ada EmbedScript docs](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#configuring-your-bot) for more information on how to get a chatter's token using ```chatterTokenCallback```.

The following instructions also abide by the assumption that any meta variable you have previously passed to Ada via EmbedScript - whether by [query paramaters](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#faq), the [setMetaFields](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#setmetafieldsmetafields-param-object) action or the [metaFields](https://github.com/AdaSupport/docs/blob/master/ada-embed.md#metafields-type-object) settings object - only consist of sanitized names.

Because unsanitized meta variable names are sanitized by Ada's backend, searching for - and removing - unsanitized variables using the endpoints described below will not be possible (and may result in unexpected issues).

Name | Description | Will be sanitized | Sanitized version
--- | --- | --- | ---
`phone_number` | Contains an underscore. Does not contain capitalized letters, special characters, whitespace or periods. **Recommended format**. | **No** | `phone_number`
`phone number` | Contains whitespace. Does not contain capitalized letters, special characters, periods or underscores. **Not recommended.** | **Yes** | `phone_number`
`PHONE NUMBER` | Contains whitespace and capitalized letters. Does not contain special characters or periods. **Not recommended.** | **Yes** | `phone_number`
`phone.number` | Contains a period. Does not contain capitalized letters, special characters, whitespace or periods. **Not recommended.** | **Yes** | `phone_number`
`phone _number` | Contains whitespace and an underscore. Does not contain capitalized letters, special characters or periods. **Not recommended.** | **Yes** | `phone__number`
`phone😡number` | Contains an emoji. Does not contain capitalized letters, special characters, whitespace, periods or underscores. **Not recommended**. | **No**, but will cause other issues. | `phone😡number`
`phone!number` | Contains a special character. Does not contain capitalized letters, whitespace, periods or underscores. **Not recommended.** | **No** | `phone!number`
`phone%20number` (passed in via [queryParams](#FAQ)) | Contains URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **Yes**| `phone_number`
`phone%20number` (passed in via [setMetaFields](#setmetafieldsmetafields-param-object) action) | Contains what looks like URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **No**| `phone%20number`
`phone%20number` (passed in via [metaFields](#metafields-type-object) settings object) | Contains what looks like URL percent encoding. Does not contain capitalized letters, ASCII whitespace, periods or underscores. **Not recommended.** | **No**| `phone%20number`

## **The Endpoints**

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