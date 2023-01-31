---
description:
  This recipe explains how to schedule content to be published for a future date depending on your front-end approach.
tags: []
skill_level:
directus_version: 9.21.2
author_override:
author: Bryant Gillespie
---

# Scheduling content for dynamic sites

> {{ $frontmatter.description }}

:::tip Author: {{$frontmatter.author}}

<!-- **Skill Level:** {{$frontmatter.skill_level}}\ -->

**Directus Version:** {{$frontmatter.directus_version}}

<!-- **Tags:** {{$frontmatter.tags.join(", ")}} -->

:::

## Explanation

This recipe explains how to schedule content to be published on a future date for dynamic sites.

Scheduling content has fewer steps for a dynamic site. Since you are calling your Directus API at the time that a
visitor requests a page from your site, all you need to do is add a filter to your query.

:::info Note

If your site is statically generated and your content fetched at build time, please
[follow the recipe for static sites](/cookbook/flows/scheduling-content-static-sites).

:::

<!-- ## Video -->
<!-- Todo -->
<!-- Need access to upload videos first -->

## How To Guide

:::tip Requirements

You’ll need to have already created a collection for your site content like `articles` or `posts` or `pages` with a
field `status` that controls the published state.

:::

### Add a field to control publish date and time

1. Under Settings, go to Data Model.

2. Choose your content [Collection](/configuration/data-model/collections).

3. [Add a new field](/configuration/data-model/fields.html#create-a-field-standard) to your content Collection.

   ![Scheduling Content - Data Model](/public/headlesscms/scheduling-content-publish-date.webp)

   a. Choose **Datetime** for the Type.

   b. For the Key, use something relevant like `date_published`.

   c. Save the Field and your Collection.

### Add some content and set a publish date

4. [Create or update an Item](/app/content/items) inside your Collection

   ![Scheduling Content - Update Content](/public/headlesscms/scheduling-content-create-content-published.webp)

   a. Set the `status` field to `published`

   b. Add a date for the `date_published` field

   c. Add the content for other fields and save the Item

### Check the published date when calling the Directus API

- When calling the API, add a [filter rule](/reference/filter-rules) that checks the `date_published` field.
- Use the `_lte` operator to filter for dates that are less than or equal the current date/time.
- You can use the the [dynamic variable](/reference/filter-rules#dynamic-variables) `$NOW` to get the current timestamp.

#### Examples

:::tip

In these examples, we're using an [AND logical operator](/reference/filter-rules#logical-operators) to only return
records that match both conditions. This provides a little more control over your published content by ensuring only
articles that have a publish date AND have the `published` state are displayed on your site.

:::

Using the [Directus Javascript SDK](/reference/sdk) (preferred)

```js
const articles = await directus.items('articles').readByQuery({
	filter: {
		_and: [
			{
				status: {
					_eq: 'published',
				},
			},
			{
				date_published: {
					_lte: '$NOW',
				},
			},
		],
	},
});
```

Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) (Javascript)

```js
const response = await fetch('https://yourdirectusurl.com/items/articles?' + new URLSearchParams({
		filter: {
			_and: [
				{
					status: {
						_eq: "published"
					}
				},
				{
					date_published: {
						_lte: "$NOW"
					}
				},
			]
		}
})
const articles = await response.json()

```

## Final Tips

**Tips**

- If you're not receiving the data you expect, double-check your [filter rule](/reference/filter-rules) syntax.
- Also be sure you have enabled the proper [permissions](/configuration/users-roles-permissions/permissions) for your
  content Collection.