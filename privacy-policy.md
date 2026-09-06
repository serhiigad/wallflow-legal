# Privacy Policy — WallFlow

**Effective date:** 2026-09-06 (added §2.9 — purchases via Google Play)
**Application:** WallFlow (`com.wallflow.app`) — an Android TV screensaver / family dashboard
with a companion phone remote.
**Developer contact:** serhiigad@gmail.com

WallFlow turns your Android TV into an ambient dashboard (clock, weather, notes, family lists,
calendar events, live backgrounds) that you control from your phone. This policy explains what
data the app processes, why, where it is stored, and how you can control it.

---

## 1. TL;DR

- We **do not sell your data** and we show **no ads** (no advertising SDKs are present in the app).
- The content you put on the board (notes, lists, events, settings) is synced between your devices
  through **Google Firebase Realtime Database**, hosted in the **EU (europe-west1)** region.
- Sign-in is **anonymous by default**. An optional Google sign-in exists **only on the TV** and
  only to let you recover the board after a reinstall.
- Weather needs coordinates: you either pick a city yourself, or (once, on first launch) the app
  derives an approximate city from your **IP address** via public geolocation APIs. The app has
  **no GPS/location permission**.
- Calendar access is **optional** and only used to display your upcoming events on the TV.
- Google Drive integration works **only with folders you explicitly provide**; the app never
  accesses your private Drive files or Google account storage.
- The optional one-time purchase (**WallFlow Pro**) is billed **entirely by Google Play**. We
  never see or store your card or payment details; the app only caches whether you own the
  product, on your device.

---

## 2. Data we process and why

### 2.1 Account identifiers (Firebase Authentication)

- **Anonymous user ID.** On first launch, every device (TV or phone) silently creates an anonymous
  Firebase Authentication account. Its purpose: let the board recognize your own devices, enforce
  access rules, and manage pairing. It is tied to the installation, not to you personally.
- **Google account (optional, TV only).** You may link a Google account on the TV so that, after
  clearing app data or a factory reset, the TV can find and reclaim its board. When you do this,
  Google provides your **email address, display name and profile photo URL**; these are stored in
  the Firebase Auth session and cached on the TV in encrypted storage. The board database itself
  only ever stores the resulting **user ID**, not your email or name.
- The phone remote does **not** require any Google sign-in.

### 2.2 Board content (Firebase Realtime Database)

Everything you create on the board is stored in our Firebase Realtime Database so the TV and your
paired phones can show and edit the same data:

| Data | Examples |
|---|---|
| Notes | note text, color, expiry time, optional author display name you type in, author device ID |
| Family lists | list title, item text, done state, order, timestamps, creator device ID |
| Calendar events | event title, description, start/end time (typed in the app, or mirrored from your device calendar if you grant the permission — see 2.5) |
| Board settings | board name, background/layout/clock choices, colors, language, weather city name and its coordinates |
| Device registry | device name, **device manufacturer and model** (e.g. "Sony BRAVIA 4K GB"), platform (Android / Android TV), role (TV host / phone remote), pairing and last-active timestamps, online status |
| Pairing data | short-lived pairing codes/tokens (they expire quickly by design), board name, and the requesting device's name/model while a pairing request awaits your confirmation on the TV |

Access to a board is restricted by database security rules: only authenticated devices that are
members of that board can read or write its content.

### 2.3 Weather and location

To show weather, the app needs a latitude/longitude pair:

- **Open-Meteo** (`api.open-meteo.com`) — receives the city coordinates and returns the forecast.
  No API key, no account, no IP logging policy beyond the provider's standard server logs.
- **Open-Meteo Geocoding** (`geocoding-api.open-meteo.com`) — receives the city name you type in
  the city search box.
- **IP-based auto-detection (optional, once).** On the TV's first launch, if no location is set,
  the app asks free geolocation services **ipapi.co** and **get.geojs.io** to resolve your **IP
  address** into an approximate city and coordinates. You can prevent this entirely by setting a
  city manually before/instead. The app holds **no Android location permission** — it never reads
  GPS.
- **OpenStreetMap Nominatim** (`nominatim.openstreetmap.org`) — if you pick a point on the map,
  the coordinates are sent there to obtain a human-readable city name.

The resulting city name and coordinates are saved in the board settings and reused for weather
refreshes.

### 2.4 Google Drive backgrounds (user-initiated only)

If you choose live video wallpapers from Google Drive, you explicitly provide a **public** Drive
folder link (or a Google Apps Script URL you set up). The app then:

- requests the file list from Google (`drive.google.com` / Drive API v3) for that folder, and
- downloads the selected video files to the TV's local cache.

Optionally you may paste **your own Google Drive API key** into the app's settings to make the
listing more reliable. That key is stored **only on your device** (in encrypted storage) and is
sent only to Google's Drive API. WallFlow has no access to your private Drive files, your Google
account's storage, or any other data in your Drive.

### 2.5 Device calendar (optional permission)

The app declares one sensitive runtime permission: **READ_CALENDAR**. If you grant it, WallFlow
reads the **titles, descriptions and times of upcoming events** (next ~30 days) from your device
calendar and mirrors them into the board database (section 2.2) so the TV can display them. If you
deny it, the calendar widget simply stays empty; nothing else is affected. Event details are read
on-device via Android's ContentProvider — no calendar service API is called by us.

### 2.6 Analytics and crash reporting

- **Firebase Analytics** collects app-usage events: which role was chosen (TV or phone), pairing
  approved/denied, and which background preset was applied — plus the standard Firebase signals
  (app version, device model, coarse location derived from IP, a Firebase installation ID). We do
  **not** attach your Firebase user ID, email or Google account to analytics events.
- **Firebase Crashlytics** collects crash reports: stack trace, device model/OS/app version, and
  the current device role. This is used solely to fix crashes.

Both are Google services; their own processing is described in Google's Privacy Policy (§4).

### 2.7 QR pairing (camera)

To join a board, the phone can scan the pairing QR code shown on the TV. This uses the **Google
Code Scanner API** (part of Google Play services). The camera image is processed on-device by
Play services; WallFlow itself never stores, uploads or receives camera images and declares no
CAMERA permission.

### 2.8 Data stored only on your device

In encrypted on-device storage (Android `EncryptedSharedPreferences`) the app keeps: your role
(TV/phone), board ID and device ID, device name, cached Google profile fields (if you signed in on
the TV), the Drive folder URL/ID and — if you added one — your Drive API key. Video wallpapers are
cached in the TV's app-private storage. If you bought WallFlow Pro, its on-device status cache
(§2.9) also lives in app-private storage.

### 2.9 Purchases and payments (Google Play)

WallFlow offers an optional one-time in-app purchase ("WallFlow Pro", product id
`wallflow_pro_founder`) through **Google Play Billing**. If you choose to buy it:

- **Payment is processed entirely by Google Play.** We never see or store your card number,
  billing address or any other payment instrument data.
- The app receives from Google Play only the **purchase state** of our single product (owned /
  pending / not owned) and the purchase token needed to confirm it. This status is cached **on
  your device** (app-private storage) so unlocked features keep working offline, and is
  re-verified with Google Play when the app starts.
- **"Restore purchases"** re-queries your Google Play purchase history for this product on the
  device; nothing beyond that query leaves your phone.
- As the developer we receive Google's standard **sales reports** for Play purchases (product,
  price, country and — where Google provides it — the buyer's name and email) for accounting,
  support and refund handling. Those records live in Google Play Console under Google's terms,
  not in our app or database.
- Analytics events about the purchase funnel (`purchase_started`, `purchase_success`,
  `purchase_fail`, `restore_success`, `paywall_shown`) carry only the product id and a response
  code — **no user id, no email, no order id** (see §2.6).

---

## 3. Permissions summary

| Permission | Required? | Purpose |
|---|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE` | yes | sync the board, fetch weather/backgrounds |
| `READ_CALENDAR` | **optional** (runtime prompt) | show upcoming events on the TV |
| Location (GPS), Camera, Contacts, Microphone, Files | **not requested** | — (see §2.3, §2.7) |

---

## 4. Third-party services we communicate with

| Service | What it receives | For what |
|---|---|---|
| Google Firebase (Auth, Realtime Database, Analytics, Crashlytics) | identifiers, board content, usage/crash events | core functionality |
| Open-Meteo (forecast + geocoding) | coordinates or city-name query | weather |
| ipapi.co / get.geojs.io | your IP address | one-time approximate city detection |
| OpenStreetMap Nominatim | coordinates | city name for a map-picked point |
| Google Drive / Apps Script URLs | the public folder ID you provided (and your own API key, if you added one) | video backgrounds you chose |
| Google Play services (Code Scanner, Sign-In) | camera frames (on-device), account picker data | pairing, optional TV sign-in |
| Google Play (Billing) | purchase state of one product, purchase token | optional one-time Pro unlock — payments are handled by Google, not by us |

These providers may log requests according to their own privacy policies:
[Google Privacy](https://policies.google.com/privacy) ·
[Firebase Terms](https://firebase.google.com/terms) ·
[Open-Meteo](https://open-meteo.com/en/terms) ·
[ipapi.co](https://ipapi.co/terms/) ·
[geojs.io](https://get.geojs.io/terms) ·
[OpenStreetMap](https://osmfoundation.org/wiki/Privacy_Policy).

We do not share your data with data brokers, advertisers, or anyone else; there is no advertising,
no ad SDKs, and no cross-app tracking in WallFlow.

---

## 5. Where and for how long

- **Location:** board data and auth identifiers live in Google Firebase infrastructure in the
  **EU region (europe-west1)**. Analytics/Crashlytics data is processed by Google under the
  Firebase Data Processing Terms.
- **Retention:** board content is kept while your board exists. You can delete any note, list,
  item or event at any time from the app; expired notes are removed automatically by the TV host;
  unpairing a device removes its registry entry. Crash reports are retained by Crashlytics for
  ~90 days (Google default). Anonymous Firebase accounts may be automatically purged by Firebase
  after a long period of inactivity (Google's configurable setting, ≥ 60 days).
- **Deletion on request:** to delete a whole board, your user record, or any residual server-side
  data, write to **serhiigad@gmail.com** from any address; tell us your board name and (if known)
  device name, and we will remove it and confirm. Uninstalling the app deletes all on-device data
  (including the WallFlow Pro status cache — §2.9). Purchase and payment records are kept by
  Google in Play Console under Google's own retention terms.

### Account and data deletion

**Delete your whole account (and all data with it):**

1. **Send an email** to **serhiigad@gmail.com** with the subject line `WallFlow account deletion`.
   In the message, name your board (e.g. "Living Room TV") and, if you know it, the device name
   shown in the app. If you linked a Google account on the TV, either write from that Google
   email or mention it — it helps us find the right record.
2. **We process the request manually**, normally within 7 days and always within 30 days, and
   reply to confirm once it is done.
3. **What is deleted:** your Firebase Authentication user (anonymous or Google-linked), the board
   and everything in it — notes, lists, calendar events, settings, device registry entries and
   pairing records.
4. **What is not deleted by this request:** files in your own Google Drive (you manage those in
   Drive yourself), and Google's own short-lived operational logs (Crashlytics crash reports
   expire after ~90 days, Firebase Analytics data expires automatically) — these are retained by
   Google under the Firebase terms, not by us.

**Delete only some of your data (without deleting the account):**

1. Open the relevant section in the app — on the phone remote or on the TV: notes, family lists,
   calendar events, or the list of paired devices.
2. Use the delete/remove action on the item you want gone: a note, a whole list or a single list
   item, an event, or a device (unpairing).
3. The change syncs to every paired device immediately and the item is removed from our servers.
   Expired notes are deleted automatically by the TV host.

---

## 6. Security

All network traffic uses HTTPS/TLS. On the device, sensitive preferences are stored in
`EncryptedSharedPreferences` (AES-256). Board access is gated by Firebase Authentication plus
Realtime Database security rules that check device membership on every read/write. No plaintext
secrets or API keys are shipped in the app.

---

## 7. Your rights

You can, at any time: **access** your board content (it is visible on your devices), **correct**
it (edit in the app), **delete** it (in the app, or by request per §5), **export** it (copy your
notes/lists; the data format is plain text in the board), **withdraw consent** (revoke the
optional calendar permission in Android settings; stop using optional Google sign-in; uninstall).

If you are in the EU/UK, you additionally have the GDPR rights of restriction, objection, and
complaint to your supervisory authority. Legal basis for processing: consent (optional features:
calendar, Google sign-in, IP detection, Drive) and legitimate interest (syncing the board you
created between your own devices, crash fixing). Requests: **serhiigad@gmail.com**.

---

## 8. Children

WallFlow is a general-audience home-screen utility and is **not directed at children under 13**.
We do not knowingly collect their data. If you believe a child's data reached us, contact us and
we will delete it.

---

## 9. Changes to this policy

We will post any change here with an updated effective date; material changes will also be noted
in the app's store listing. The canonical copy of this document lives in the WallFlow project
repository (`docs/privacy-policy.md`).

## 10. Contact

WallFlow developer — email: **serhiigad@gmail.com**
