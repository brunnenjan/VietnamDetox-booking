# Vietnam Detox booking app

## URL preselection

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
