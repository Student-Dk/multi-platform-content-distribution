# Multi-Platform Content Distribution (n8n)

An n8n workflow (about 140 nodes) that publishes one post to several social platforms from a single form submission. Write the post once, pick the platforms, and the workflow routes it by post type.

## What it does

1. A form trigger collects the caption, the media (image, video or several images) and the platforms to post to. If no platform is selected, it posts to every platform that supports that post type.
2. IF nodes detect the post type: text, image, video or carousel.
3. A Code node (JavaScript) builds the list of valid target platforms for that post type, for example Instagram is skipped for text-only posts. A Split Out node turns the list into one item per platform.
4. A Switch node sends each item to that platform's branch. Branches are independent, so a new platform can be added without changing the others.
5. Each branch calls the platform's API (Facebook Graph API, Instagram and Threads containers, LinkedIn upload and publish, Tumblr, Mastodon, YouTube).
6. Cloudinary hosts media that Instagram and Threads need as public URLs (also used for Tumblr video). Wait nodes and a status-polling loop handle asynchronous processing, such as LinkedIn asset status and Instagram containers.

## Post types per platform

| Platform | Text | Image | Video | Carousel |
| --- | --- | --- | --- | --- |
| LinkedIn | Yes | Yes | Yes | Yes |
| Facebook | Yes | Yes | Yes | Yes |
| Instagram | - | Yes | Yes | Yes |
| Threads | Yes | Yes | Yes | Yes |
| Tumblr | Yes | Yes | Yes | Yes |
| Mastodon | Yes | Yes | Yes | Yes |
| YouTube | - | - | Yes | - |

## Setup

1. In n8n, choose **Import from file** and select `multi-platform-content-distribution.json`.
2. Reconnect credentials on the nodes that need them (Facebook Graph API, YouTube OAuth2, generic OAuth2 and Bearer auth).
3. Replace the placeholders in the HTTP Request nodes:
   - `YOUR_META_ACCESS_TOKEN`: Meta access token for Instagram and Facebook calls
   - `YOUR_MASTODON_ACCESS_TOKEN`: Mastodon access token
   - `YOUR_FACEBOOK_PAGE_ID`, `YOUR_INSTAGRAM_USER_ID`, `YOUR_THREADS_USER_ID`, `YOUR_LINKEDIN_ID`: account IDs
   - `YOUR_BLOG`: Tumblr blog name
   - `YOUR_CLOUD_NAME`: Cloudinary cloud name (the workflow uses an unsigned upload preset named `n8n_upload`)
   - `YOUR_N8N_INSTANCE` / `YOUR_FORM_PATH`: your n8n URL and form path in the form's result pages
4. Activate the workflow and open the form URL to post.

All secrets and personal identifiers were removed from this export.

## Limitations

- Rule-based routing only. The workflow does not use AI to make decisions.
- No error handling or retries yet: a failed API call on one platform is not retried and raises no alert.
- Some access tokens are set directly in HTTP Request nodes. They should move into n8n credentials.

## Planned improvements

- AI-based caption generation
- Scheduled posting at the best time
- Analytics dashboard for post performance
