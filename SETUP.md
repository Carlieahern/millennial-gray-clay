# Millennial Gray Clay — setup

The whole site is one file: `index.html`. Design, sections, item types, items,
prices, shipping and pinned posts all live in Firebase, so you edit them from
the site itself and visitors see the change.

Right now it runs in **local preview mode** — it works, but nothing saves to the
cloud until you do step 1.

---

## 1. Paste in your Firebase keys (the only thing I can't do for you)

I filled in everything that follows from your project id. Three values are
unique to your project and only you can see them.

1. Firebase console → your **millennial-gray-clay** project
2. Gear icon → **Project settings** → scroll to **Your apps**
3. If there is no web app yet, click the **`</>`** (Web) icon, nickname it
   `millenialgrayclay`, and skip "Firebase Hosting" for now
4. You'll see a `firebaseConfig` block. Copy these three values out of it.

Open `index.html`, scroll to the bottom, and replace the three placeholders:

```js
const FIREBASE_CONFIG = {
  apiKey:            "PASTE_API_KEY_HERE",        // <- AIza...
  authDomain:        "millennial-gray-clay.firebaseapp.com",
  projectId:         "millennial-gray-clay",
  storageBucket:     "millennial-gray-clay.firebasestorage.app",
  messagingSenderId: "PASTE_SENDER_ID_HERE",      // <- a long number
  appId:             "PASTE_APP_ID_HERE"          // <- 1:123...:web:abc...
};
```

These keys are *meant* to be public — they identify your project, they don't
grant access. Step 3 is what actually protects your data.

## 2. Turn on the two Firebase services

**Firestore Database** → Create database → **Production mode** → pick a region
close to you (e.g. `us-central1`). Region can't be changed later.

**Authentication** → Get started → **Email/Password** → Enable → Save.
Then **Users** tab → **Add user** → your email and a password. That is your
Studio login. Don't enable any other sign-in method: the only accounts that
exist are the ones you create here, which is exactly what you want.

## 3. Lock it down with the rules

Firestore Database → **Rules** tab → delete what's there → paste the whole
contents of `firestore.rules` → **Publish**.

Until you do this, production mode blocks everything and the site will show
"Firestore rules are blocking reads". After it, the rules say: anyone can read
your shop and send an inquiry; only your signed-in account can change anything
or read the inquiries.

## 4. Put it online

Any static host works, since it's one file. Two easy options:

**Vercel** (same as your other projects) — drag the `Art Shop` folder onto
vercel.com/new, or `vercel --prod` from inside it.

**Firebase Hosting** — already in the project you just set up:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

When asked for the public directory, say `.` and answer **No** to rewriting
to index.html as a single-page app.

Then: Firebase console → Authentication → Settings → **Authorized domains** →
add whatever domain you end up on. Sign-in is blocked on domains not listed.

## 5. Point millenialgrayclay at it

Buy the domain wherever you like, then add it in your host's domain settings
(Vercel → project → Domains, or Firebase → Hosting → Add custom domain) and
follow the DNS records it gives you.

One thing to decide: your Firebase project is **milleNNial**-gray-clay (two
n's, the standard spelling) but you wrote the site name as **millenialgrayclay**
(one n). Both are available as domains in principle — just pick deliberately,
because the one you buy is the one on your packaging forever. The project id
itself is invisible to customers, so it doesn't have to match.

---

## Using the Studio

Click **Studio** at the bottom of any page, or press `Ctrl+Shift+E`.
Sign in with the account from step 2. `Ctrl+S` saves.

| Tab | What it's for |
|---|---|
| **Look & feel** | Site name, 8 colors (or a one-click preset), fonts, corners, texture, dividers, photo framing, spacing, page width |
| **Sections** | The tabs across the top. Add, rename, reorder, hide, delete. Seven kinds: hero, shop grid, about, socials, contact form, text block, photo gallery |
| **Items** | Everything for sale. Photos, price, status, draft/published, and per-item options |
| **Item types** | Mugs vs. originals vs. prints — each type has its own detail fields |
| **Price calculator** | What to charge, worked backwards from real costs |
| **Shipping** | Delivery options, packaging cost, free-shipping threshold, local pickup |
| **Socials** | Profile links, plus posts you pin by pasting embed codes |
| **Inquiries** | Everyone who asked to buy something, with an unread count and alerts |
| **Setup** | Backup/restore, reset the design |

### The price calculator

Enter what the piece actually cost you (materials line by line, hours spent,
kiln firing or framing) and it works **backwards** from the profit you asked
for to the price that gets you there — after fees.

- **You pay yourself per hour** — your wage. This is a cost, not profit.
- **Profit margin** — what you want left over *after* paying yourself. 40% is a
  normal craft starting point.
- **Overhead %** — costs that aren't per-piece: studio, power, website, tools
  wearing out. 10–15% to start.
- **Fees %** — what the payment app keeps. ~3% for most cards.

"You keep" is profit *on top of* your hourly pay. There's also a box to type
any price you like and see what margin it actually leaves you — useful for
checking a number you already had in mind.

Open an item → **Price this with the calculator** → **Use this price** pushes
the number onto the item and remembers the costing behind it.

### Giving an item options

Most pieces are bought exactly as they are. For the ones that aren't, open the
item and switch on **This one has choices to make**.

There are only two kinds of group, and everything is built from them:

| Kind | What it does | Good for |
|---|---|---|
| **Pick one** | A single dropdown | Size, glaze, finish, stand |
| **Pick how many, then pick each** | "How many?" from 1 to a number you set, then that many dropdowns | The donut sculpture — choose 3, then choose each donut |

Every entry in a dropdown has an **adds to price** box. Leave it at 0 for the
ones that cost the same. A "how many" group also has **each one after the
first adds** — the item's own price covers the first one.

So a $40 donut tower, $12 per extra donut, with gold leaf at +$15:
three donuts, one of them gold, comes to $40 + $12 + $12 + $15 = **$79**.
If you'd rather charge flat per unit instead, put the full price in the
"each one after the first" box and the sums work the other way round.

The price on the page updates as the customer chooses, and what they picked
comes through on the inquiry with the total already worked out — in Studio →
Inquiries and in the alert email, if you've set that up.

**Letting them type something.** Switch on **lets them type** against any
entry — call it "Custom" — and a text box appears when they pick it. Set
**characters they get** to whatever suits that piece; it's per group, so a
short engraving and a long description can live on different items.

**Everything on show has to be answered.** Nothing is pre-selected — every
dropdown starts on `-- choose --`, and **Inquire to buy** won't go through
until they've picked. A text box that a "lets them type" entry opened has to
be filled in too, and spaces don't count. Whatever's missing gets marked in
red and listed above the button, so an inquiry can never reach you with a gap
in it, and no order can arrive carrying a default the buyer never looked at.

That means there's no way to offer an optional extra as a dropdown. If
something is genuinely take-it-or-leave-it, make "No thanks" one of the
entries at +$0 — they still have to choose it, which is the point.

**Sizes** are just a "pick one" group. The **+ Sizes** button makes one
pre-filled with Small / Medium / Large so you only have to edit the prices.

Switching the whole thing back off hides the options from your site but keeps
the groups, so you can turn them on again later without rebuilding them.

### The message box

Every listing has an **Anything you want to say** box, whether or not it has
options — nothing to switch on. It's optional, capped at 1,500 characters,
and it carries through to the inquiry form so nobody has to retype it. It
arrives as the message on the inquiry, same as before.

> **Republish `firestore.rules` before you rely on this** (section 3). The
> rules now have to let an inquiry carry the choices, and they got stricter
> at the same time: an inquiry may only contain the fields the form actually
> sends, and it can't arrive pre-marked as already seen. Until you republish,
> inquiries with options on them will be rejected.

### Pinning a social post

Instagram: open the post in a browser → the **⋯** menu → **Embed** → Copy embed
code. TikTok: **Share** → **Embed**. Paste the whole block into
Studio → Socials → Pin a post.

Posts show the real thing, but they come from Instagram/TikTok, so if you delete
a post there it disappears here too. Only paste codes you copied yourself —
embed code is live HTML, so a block from somewhere you don't trust can do
anything on the page.

### Photos

Uploaded photos get shrunk in your browser and stored with the item, so there's
no Firebase Storage bill and nothing to configure. The trade-off is a 1MB cap
per item — roughly 4–5 photos. The item editor shows you the size as you go. For
a long gallery, paste image URLs instead.

### Backups

Studio → Setup → **Download backup** gives you one JSON file with your whole
site in it. Worth doing before any big redesign.

---

### Knowing when an inquiry arrives

This is already on. Nothing to set up, nothing to pay for.

Any inquiry you haven't looked at yet shows as a **number** in three places,
and all three only appear when you're signed in — a visitor never sees them:

- on the **Studio** link at the foot of your own site
- on the **Inquiries** tab inside Studio
- in the browser tab itself, as `(2) Millennial Gray Clay`, so you can see it
  from another tab without clicking in

The tick button on a row marks it seen and drops the count; the arrow puts it
back to new if you'd rather deal with it later. **Mark all as seen** clears
the lot. "Seen" is stored on the inquiry, not in your browser, so the count
is the same on your phone and your laptop.

If you happen to have the site open in a tab when one lands, you also get a
chime and, if you switch it on, a pop-up in the corner of your screen.
Both live in Studio → Inquiries, under *While you have the site open*. They
are remembered **per device** rather than in your site settings, so your
laptop can chime while your phone stays quiet, and nothing about them is
published. **Show me one** fires a fake alert so you can see what it looks
like without putting a test inquiry in your database.

Those two only work while the site is actually open. Close it and you're back
to the counters, which are always right whenever you come back.

### Getting inquiries emailed to you (optional, needs Blaze)

You don't need this — the counters above cover it for free. This is for when
you want the whole inquiry pushed to your inbox without opening the site at
all. Four steps, roughly 15 minutes, and it needs a card on file.

**1. Upgrade Firebase to Blaze.** Console → the gear by *Project Overview* →
**Usage and billing** → **Modify plan** → Blaze. It needs a card. Your actual
cost will be nothing — the free monthly allowance is millions of operations
and you will use dozens — but Blaze is pay-as-you-go, so **set a budget alert
while you are in there**. A few dollars is plenty; it emails you long before
anything real could happen.

**2. Get an SMTP account.** This is whatever actually sends the mail. Gmail
works: turn on 2-step verification, then make an **app password** and use

```
smtps://you%40gmail.com:THEAPPPASSWORD@smtp.gmail.com:465
```

Note `%40` where the `@` in your address goes. If you type a literal `@`
there the URI splits in the wrong place and the extension fails to connect,
with an error that does not explain itself. Resend, SendGrid and Mailgun all
have free tiers and are less fussy about volume than Gmail.

**3. Install the extension.** Console → **Extensions** → find
**Trigger Email from Firestore** → Install. When it asks:

| It asks for | Answer |
|---|---|
| Email documents collection | `mail` |
| SMTP connection URI | the string from step 2 |
| Default FROM address | your shop address |
| Users / templates collection | leave blank |

The collection **must** be `mail` — that is the name the website writes to and
the name the security rules allow.

**4. Switch it on in Studio.** Inquiries tab → **Send alerts to** → your
address → Save. Then send yourself a test inquiry from the live site.

Republish `firestore.rules` as well (section 3) — the `mail` rules are new.

#### What to know about how this works

The website writes the email into a `mail` collection from the visitor's
browser, and the extension sends whatever it finds there. Two consequences
worth understanding:

- **Your alert address is public.** The browser has to know where to send it,
  so the address sits in your site settings, which anyone can read. Use a shop
  address or an alias, not a private one.
- **The email is best-effort.** It is queued a split second after the inquiry
  is saved. If the visitor's connection dies in that instant, you get no
  email — but the inquiry is still safely in Studio. The list is the record;
  the email is the nudge.

Both of those go away if the queueing moves to a Cloud Function instead of the
browser. That needs Node.js installed to deploy it, which this machine does not
have.

---

## Things this deliberately does not do

- **No card checkout.** Buyers send an inquiry and you invoice them. Adding
  Stripe later means a small server piece, so it stops being one file.
- **No automatic social feed.** Pulling your posts in automatically needs a Meta
  developer app and tokens that expire every 60 days. Pasted embeds never break
  on their own.
- **No stock counts.** Each item is one thing with a status. If you start
  selling multiples of the same mug, that's the next thing to add.
