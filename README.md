# Vietnam Detox booking app

## Payment methods

The English payment step offers international cards (selected by default) and
OnePay domestic QR. The QR option explains that a Vietnamese bank account is
required. Vietnamese keeps QR as its payment method; German keeps international
cards. The selected method is used for both the booking and OnePay requests and
is restored after returning from OnePay, including failed-payment retries.

## Guest gender

Step 2 requires a gender selection for each guest (English, Vietnamese and German
labels). The review step displays it, and the booking payload includes
`guestDetails.guest1.gender` / `guest2.gender` as `male` or `female`.

Sync `index.html` together with the updated Kendo plugin booking API and guest
email helper. The API validates gender before creating records, stores customer
and registration-specific values, and returns them in booking recovery. Email
salutations use Mr./Ms. in English or Anh/Chị in Vietnamese. Existing bookings
without gender remain recoverable; their gender field is read-only and corrections
go through staff rather than silently changing only the browser state.

## Phone country codes

Both guests' phone numbers have a searchable country picker covering every country
and territory (`PHONE_COUNTRIES`: ISO code, ITU dial code and English name). Names are
shown in the booking language via `Intl.DisplayNames`. The search matches English or
translated names without accents ("viet", "duc"), ISO codes and dial codes ("44",
"+84"). Arrow keys, Enter and Escape work, and the list scrolls to the current country.
Flags are emoji. Windows has no flag emoji, so there the app loads Twemoji's flag-only
font from jsDelivr (`country-flag-emoji-polyfill`).

- Guest 1's code follows the country of residence typed in step 2 (English, Vietnamese or
  German names, plus aliases such as "USA" and "UK") until a code is picked by hand.
- Guest 2 follows guest 1 until it is changed separately.
- Pasting "+49 151…" or "0049 151…" into the number selects that country when the
  field loses focus.

The booking payload now carries the code. `guestDetails.*.phone` and `purchase_phone`
are sent as `"+84 912 345 678"`, so the API stores the number with its country code.
The national trunk 0 is dropped, except for Italy, San Marino and the Vatican. Before
this, only the local number was sent and the selected code was lost. No API change is
needed.

## URL preselection

The app requests `/wp-json/retreats/v1/all?lang=…&context=booking`. This API
response excludes Hồi Retreat and its translations because bookings are handled
by Fusion. Sync `index.html` with the plugin's `includes/api/retreats/queries.php`
for this filter to take effect. The shared `/all` response without this context
and individual retreat endpoints still include Hồi for the website and portal.

The first booking step accepts these optional query parameters:

- `retreat_id`: retreat post ID returned by the current-language response from
  `/wp-json/retreats/v1/all`
- `start_date`: API start date in `MM/DD/YYYY` format
- `package_sku`: package SKU returned for the selected retreat

Example for the first currently available Yên Retreat date and its one-person
Wellness Bungalow package:

`/booking/?retreat_id=2244&start_date=09%2F23%2F2026&package_sku=WELBUN-1B-1P`

The same parameters work on translated booking-page paths. A two-person package
also selects two guests automatically so that the requested package remains
visible and selected.

Invalid or conflicting values are handled independently. A valid retreat and
package remain selected, while an invalid date falls back to that retreat's
first available date. Direct visits without parameters retain the normal first
available retreat, date, and package defaults.

A valid booking link shows **only** its retreat, with that retreat's dates,
guests, packages and transfers, so guests coming from a retreat page cannot pick
another retreat by accident. Package-only links do the same for their owning
retreat. Direct visits (no parameters) list every bookable retreat in API order.
A `retreat_id` that is unknown or has no upcoming dates falls back to the full list.

Retreats without upcoming dates (including private retreats with only historical
dates) are hidden from the booking list. The shared retreats API is unchanged so
other pages can still display them.
