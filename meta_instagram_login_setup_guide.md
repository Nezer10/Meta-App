# Meta Developers Setup Guide
## Instagram API with Instagram Login for `@ziusudra_co`

> **Purpose:** Create a Meta app, enable the Instagram API with Instagram Login, add `@ziusudra_co` as an Instagram tester, and generate the first Instagram access token for a read-only website integration.

---

## 1. Before You Start

Make sure you have:

- A Meta/Facebook account that can create Developer apps.
- `@ziusudra_co` configured as an **Instagram Professional account** (Business or Creator).
- Access to the `@ziusudra_co` Instagram account so you can accept the tester invitation.
- Your website/project does not need to be connected yet; this guide focuses on getting the first working API token.

### Recommended API architecture

For this project, use:

```text
Instagram Login
        ↓
instagram_business_basic
        ↓
graph.instagram.com
        ↓
Instagram User Access Token
```

Do **not** build this around:

```text
Facebook Login
Facebook Page APIs
Old Instagram Basic Display API
Instagram oEmbed
```

---

# 2. Open Meta for Developers

Go to:

**https://developers.facebook.com/**

Log in with the Meta/Facebook account that will own the application.

Then open:

```text
My Apps
   ↓
Create App
```

Meta's current app-creation UI is use-case driven, so you may see **Add use cases** instead of older product-first wording.

---

# 3. Create the Meta App

Enter the basic app information.

Example:

```text
App Name:
Ziusudra Store Instagram

App Contact Email:
your-email@example.com
```

The app name is an internal Meta Developer application name, so you can choose any clear name.

## Choose the Instagram use case

When Meta asks what the app is for, look for:

```text
Content management
```

and select:

```text
Manage messaging & content on Instagram
```

Then continue through the wizard and create the app.

Depending on the dashboard version, you may see different button labels such as:

```text
Next
Create App
Go to Dashboard
```

---

# 4. Business Portfolio Screen

Meta may ask:

> Which business portfolio do you want to connect to this app?

You may see options such as:

- Choose an existing Business Portfolio
- Create a Business Portfolio
- **I don't want to connect a business portfolio yet**

For a simple one-company development setup, you can choose:

```text
I don't want to connect a business portfolio yet
```

when that option is available.

Continue until you reach the app dashboard.

---

# 5. Open the Instagram API Setup

Inside the app dashboard, look for:

```text
Instagram
```

Then open:

```text
API setup with Instagram business login
```

Some dashboard versions may shorten the wording to:

```text
API setup with Instagram login
```

This is the setup you want.

> **Important:** Meta's UI has changed over time. The exact menu labels may vary slightly, but the target is the Instagram-login-based API setup, not Facebook Login.

---

# 6. Enable `instagram_business_basic`

Find the permissions/configuration area.

Depending on the dashboard version, this may be under:

```text
Use cases
   ↓
Manage messaging & content on Instagram
   ↓
Customize
   ↓
Permissions and features
```

Enable:

```text
instagram_business_basic
```

For your current read-only website integration, you do **not** need to add:

```text
instagram_business_content_publish
instagram_business_manage_comments
instagram_business_manage_messages
instagram_business_manage_insights
```

Keep the permission scope as small as possible.

---

# 7. Add `@ziusudra_co` as an Instagram Tester

This is required while the app is being tested in development.

There are two places where the current Meta dashboard may expose the tester flow.

## Route A — App Roles

Go to:

```text
App Dashboard
    ↓
App Roles
    ↓
Roles
```

Choose:

```text
Add People
```

Then look for:

```text
Instagram Tester
```

Enter:

```text
ziusudra_co
```

and send the invitation.

## Route B — Instagram Setup Page

Some current dashboard versions expose the account directly from:

```text
Instagram
    ↓
API setup with Instagram business login
    ↓
Generate access tokens
    ↓
Add account
```

If your dashboard uses this route, follow the account-association flow there.

---

# 8. Accept the Tester Invitation on Instagram

Adding the account in Meta is only half of the process.

Log in to Instagram **as `@ziusudra_co`** and find the tester invitation.

A commonly reported path is:

```text
Instagram
   ↓
Settings
   ↓
Website permissions
   ↓
Apps and websites
   ↓
Tester invites
```

Accept the invitation for your Meta app.

## The required sequence

```text
Meta Developer Dashboard
        ↓
Add @ziusudra_co as Instagram Tester
        ↓
Instagram account
        ↓
Accept tester invitation
```

If the invitation is not accepted, token generation may fail with a permission/developer-role error.

---

# 9. Return to the Instagram API Setup

Go back to:

```text
Meta Developers
   ↓
Your App
   ↓
Instagram
   ↓
API setup with Instagram business login
```

Find:

```text
Generate access tokens
```

You should see the Instagram account you added, for example:

```text
Instagram accounts

@ziusudra_co
                     Generate token
```

---

# 10. Generate the First Access Token

Click:

```text
Generate token
```

next to:

```text
@ziusudra_co
```

Meta will open Instagram authentication.

If prompted, sign in as `@ziusudra_co` and authorize the application.

Depending on the dashboard version, you may see an additional confirmation or acknowledgement before the token is displayed.

Once successful, Meta will provide an Instagram access token that typically looks similar to:

```text
IGQ...
```

Copy it securely.

---

# 11. Store the Token in Your Project

For local development, your `.env.local` can contain:

```env
INSTAGRAM_ACCESS_TOKEN=IGQxxxxxxxxxxxxxxxxxxxxxxxx
INSTAGRAM_USER_ID=me
INSTAGRAM_API_VERSION=v26.0
```

### Security

Never:

- Commit `.env.local` to Git.
- Put the token inside browser/client-side JavaScript.
- Expose the token in a public API response.
- Put your Meta/Instagram app secret in frontend code.

The access token should remain **server-side**.

---

# 12. Don't Confuse Meta Credentials

The Meta dashboard can expose several different credentials.

You may encounter:

```text
Meta App ID
Meta App Secret

Instagram App ID
Instagram App Secret

Instagram User Access Token
```

For your immediate `/media` API test, the important credential is:

```text
Instagram User Access Token
```

The app secret is needed for server-side OAuth/token exchange operations, not for exposing the token to a browser.

---

# 13. Test the Token Immediately

Before integrating it into the website, verify that the token works.

## Test `/me`

Run:

```bash
curl -G "https://graph.instagram.com/v26.0/me" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  --data-urlencode "fields=id,username"
```

A successful response should look similar to:

```json
{
  "id": "1784xxxxxxxxxxxx",
  "username": "ziusudra_co"
}
```

## Test `/media`

Then test the actual media endpoint:

```bash
curl -G "https://graph.instagram.com/v26.0/me/media" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  --data-urlencode "fields=id,caption,media_type,media_product_type,media_url,thumbnail_url,permalink,timestamp" \
  --data-urlencode "limit=20"
```

If this works, your Meta-side configuration is basically complete for the first read-only integration.

---

# 14. Recommended Dashboard Structure

Conceptually, the dashboard should look like this:

```text
Meta for Developers
│
└── My Apps
    │
    └── Ziusudra Store Instagram
        │
        ├── Use cases
        │    └── Manage messaging & content on Instagram
        │          └── instagram_business_basic
        │
        ├── Instagram
        │    └── API setup with Instagram business login
        │          │
        │          └── Generate access tokens
        │                 └── @ziusudra_co
        │                         └── Generate token
        │
        └── App Roles
             └── Roles
                  └── Instagram Tester
                       └── @ziusudra_co
```

Meta may move individual controls around as the dashboard evolves, but these are the important pieces.

---

# 15. Avoid the Older Facebook-Based Flow

You may find older tutorials instructing you to do this:

```text
Create Facebook Page
        ↓
Connect Instagram to Page
        ↓
Facebook Login
        ↓
instagram_basic
        ↓
Facebook Graph API
```

That is **not** the architecture recommended for this project.

Use:

```text
Instagram Login
        ↓
instagram_business_basic
        ↓
graph.instagram.com
        ↓
Instagram User Access Token
```

---

# 16. Final Checklist

Before moving to your Next.js implementation, verify every item below:

```text
[ ] Meta Developer account exists
[ ] Meta app created
[ ] "Manage messaging & content on Instagram" enabled
[ ] Instagram API setup opened
[ ] instagram_business_basic enabled
[ ] @ziusudra_co is a Business or Creator account
[ ] @ziusudra_co added as Instagram Tester
[ ] Tester invitation accepted on Instagram
[ ] @ziusudra_co appears under Generate access tokens
[ ] Generate token succeeded
[ ] Token starts with IG...
[ ] /me request works
[ ] /me/media request works
```

Once all of these are checked, Meta's dashboard configuration is essentially finished for the initial read-only integration.

---

# 17. Next Implementation Step

After obtaining the token, the recommended website architecture is:

```text
Browser
   ↓
Your Next.js backend
   ↓
Instagram Graph API
```

For the website itself, a clean production setup would expose internal endpoints such as:

```text
/api/instagram/reels
/api/instagram/stories
```

with:

- Server-side token usage
- Caching
- Pagination handling
- Automatic long-lived-token refresh
- Filtering of the latest five Reels
- Separate handling of active Stories

That keeps all Meta credentials private while giving the storefront a simple JSON API.
