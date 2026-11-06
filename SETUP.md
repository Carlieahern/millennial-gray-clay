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
| **Items** | Everything for sale. Photos, price, status, draft/published |
| **Item types** | Mugs vs. originals vs. prints — each type has its own detail fields |
| **Price calculator** | What to charge, worked backwards from real costs |
| **Shipping** | Delivery options, packaging cost, free-shipping threshold, local pickup |
| **Socials** | Profile links, plus posts you pin by pasting embed codes |
| **Inquiries** | Everyone who asked to buy something |
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

### Getting inquiries emailed to you

Out of the box, an inquiry lands in Studio → Inquiries and nowhere else. To
get the whole thing emailed to you instead, four steps, roughly 15 minutes.

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
