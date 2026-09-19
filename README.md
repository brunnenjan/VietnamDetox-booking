# Vietnam Detox booking app

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

The retreat selected by a valid booking link appears first in the retreat list;
the remaining retreats keep their API order. Package-only links also move their
owning retreat to the top. Selecting another retreat within the booking app does
not reorder the cards. Direct visits keep the API order.

Retreats without upcoming dates (including private retreats with only historical
dates) are hidden from the booking list. The shared retreats API is unchanged so
other pages can still display them.
