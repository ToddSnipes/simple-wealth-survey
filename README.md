# Simple Wealth — Post-Event Survey

Two files: `index.html` (the page) and `banner.jpg` (the header, cropped from the flyer).
Keep them in the same folder.

## Before you send: one edit

Open `index.html`, search for `EDIT:` and put in the event date. Everything else
(event name, Melbourne FL, your names, Chad's number) is already filled in from the flyer.

## Hosting — pick one

### Option A: Netlify (fastest, responses collected for you, free)

1. Go to https://app.netlify.com/drop
2. Drag this whole `event-survey` folder onto the page.
3. You get a URL like `https://something-random.netlify.app`. Rename it under
   Site settings → Change site name (e.g. `simplewealth-survey.netlify.app`).
4. Responses appear at **Forms → event-survey** in the Netlify dashboard. Export CSV
   from there. Turn on email notifications under Forms → Form notifications so each
   submission lands in your inbox.

Free tier: 100 submissions/month. Nothing to configure — the form already has
`data-netlify="true"`.

### Option B: Google Sheet (responses straight into a spreadsheet)

Use this if you'd rather host on your own site/GoDaddy/etc., or just want a Sheet.

1. Create a new Google Sheet. Put these headers in row 1, in this order:

   ```
   timestamp, name, email, phone, contact_pref, experience, rating, rating_why, takeaway_1, takeaway_2, takeaway_3, quote_ok, topics, topics_other, source, interests, interests_other, markets, time_per_week, capital, obstacles, goal_6mo, goal_1yr, goal_5yr, now, call, referral_name, referral_contact, anything_else
   ```

2. Extensions → Apps Script. Delete what's there and paste:

   ```javascript
   function doPost(e) {
     var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
     var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
     var p = e.parameter, multi = e.parameters;
     var row = headers.map(function (h) {
       if (h === 'timestamp') return new Date();
       // checkbox questions arrive as arrays -> join with " | "
       if (multi[h] && multi[h].length > 1) return multi[h].join(' | ');
       return p[h] || '';
     });
     sheet.appendRow(row);
     return ContentService.createTextOutput('ok');
   }
   ```

3. Deploy → New deployment → type **Web app** → Execute as **Me**, Who has access
   **Anyone** → Deploy. Copy the web app URL.
4. In `index.html`, find `var SHEETS_ENDPOINT = "";` and paste the URL between the quotes.
5. Upload `index.html` + `banner.jpg` anywhere (your website, GitHub Pages, Netlify, etc.).

## Sending it out

Copy for the email and text is in `outreach-copy.md`. Replace `[SURVEY LINK]` with your URL.
For texts, shorten the link first (bit.ly or similar) — a long netlify URL looks like spam.

## Reading the results

The questions that matter most for follow-up, in order:

- **Q13 "Does any of this apply right now"** — anyone who checked a deal/capital box: call today.
- **Q14 "Want a call"** — "this week" people get a call within 48 hours.
- **Q7 interests × Q10 capital** — Private Money / Passive Notes + $100k+ = your lender list.
- **Q7 interests × Q9 time** — Referral Partner + under 5 hrs = referral-partner onboarding.
- **Q11 obstacles** — the top two answers are your next event's topic.
- **Q4 takeaways with quote_ok = Yes** — testimonials for the next flyer.
