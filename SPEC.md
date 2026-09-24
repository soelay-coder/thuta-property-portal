# Property Portal feature specification

This document describes product behavior across the web app, API, and mobile app. It may change as the product evolves. Stable project boundaries, stack choices, and architecture are in `AGENTS.md`.

## Product goals

- Let anyone browse and search public properties without signing in.
- Support Yangon, Mandalay, and Bago, with Buy, Sell, and Rent listing intents.
- Let account holders create listings, submit them for administrator review, and manage their own listings.
- Let administrators review listings and inspect user accounts.
- Provide a consistent API contract for web and mobile clients.

The MVP does not include payments, deposits or escrow, in-app chat, map search or geocoding, identity verification, reviews, ratings, favorites, saved searches, alerts, or recommendations. Keep data and UI labels ready for future localization; English is the initial UI language.

## Users and permissions

### Public visitor

- Browse home, search results, and listing details without an account.
- Search and filter properties and view the contact details intentionally published by the lister.
- Start account creation or sign-in.

### Authenticated user

- Create draft listings, add and reorder photos, and submit listings for review.
- Edit and delete their own listings, unpublish their own published listings, and view a dashboard of their listings.

### Administrator

- Review owner submissions, approve them for public search, reject them with a review note, restore rejected listings to review, and remove published listings.
- Inspect user accounts, roles, and listing counts.

The API enforces permissions and ownership server-side.

## Listing behavior

Support these listing intents: Buy, Sell, and Rent. Support these property types: Apartment, House, Condo, Land, Commercial, and Room. Initial supported cities are Yangon, Mandalay, and Bago. Suggested townships include:

- Yangon: Kamaryut, Bahan, Yankin, Mayangone, Hlaing, Sanchaung, Dagon, and Tamwe.
- Mandalay: Chanayethazan, Chanmyathazi, Aungmyaythazan, Mahaaungmyay, and Pyigyidagun.
- Bago: Bago Township, Thanatpin, Waw, and Kawa.

Listings move through Draft, Pending Review, Published, Paused, Rejected, Expired, and Removed states. Only Published listings appear in public discovery. A listing becomes public only after administrator approval.

To submit a listing, require a title of 10–120 characters, a description of 50–5,000 characters, a supported city, property type, positive integer price in MMK, rent period for Rent, at least one photo, and contact name and phone. Bedroom and bathroom values, when provided, must be non-negative integers. Drafts may be incomplete.

## Public browsing and search

The home page highlights Yangon, Mandalay, and Bago and provides search by keyword or township, intent, city, and optional minimum and maximum price.

Search supports free text across title, description, township, and address; intent; city; township; property type; minimum and maximum price; bedroom and bathroom counts; furnished state; sorting by newest or price in either direction; and pagination. Prices use MMK in the MVP. Search filters are shareable through URL query parameters and survive refresh. On narrow screens, filters open in a sheet or drawer.

Listing cards show the primary photo or a graceful fallback, intent, property type, title, city and township, price and rent period where applicable, bedroom and bathroom counts where applicable, and publication date or status. Keep image proportions consistent and avoid layout shift as photos load.

Listing detail pages show an accessible photo gallery, title and intent, price and rent period, location and public address text, property facts, full description, amenities, publication information, and lister contact details. Include loading, not-found, unavailable, and failed-image states. Provide a report-listing action or placeholder. Do not claim a listing is verified unless verification is represented by the service.

## Account and listing journeys

### Browse a property

1. A visitor opens the home page and selects a city, intent, or search phrase.
2. Search results reflect the selected criteria and allow additional filters and sorting.
3. The visitor opens a listing and uses its displayed contact action.

### Post a property

1. The user signs up or signs in and opens “Post a property”.
2. They enter intent, type, location, price, features, description, photos, and contact details.
3. They can save a draft and submit it for review.
4. An administrator approves or rejects it. It appears in public search only after approval.

### Manage a property

1. The owner opens a dashboard showing their listings and statuses.
2. They edit or unpublish a listing, or delete a draft.
3. A published listing can be removed from public view by its owner or an administrator.

The dashboard shows image, title, status, intent, location, price, updated date, and view/edit/unpublish/delete actions. Use a responsive table on larger screens and cards on narrow screens.

## API capabilities

The versioned API supports these feature groups:

- Account creation, sign-in, sign-out, and current-user lookup.
- Public listing search and detail, supported locations, and property types.
- Owner listing creation, retrieval, editing, submission, unpublishing, deletion, and photo upload, removal, and reordering.
- Administrator listing review and user inspection.

Success responses use a JSON `data` envelope and may include `meta`. Errors provide a code, human-readable message, and field details when relevant. Listing search accepts the supported filters and returns page, page size, total count, and total pages. Public discovery returns Published listings only.

## Photo behavior and seeded content

Owners can add photos, remove them, and set display order. Each photo has useful alt text. The web listing form supports up to 12 JPG, PNG, WEBP, GIF, or AVIF images, up to 8 MB each.

Development seed content includes at least 18 listings, with at least six in each supported city, a mix of intents and property types, at least one listing with one photo, one with three photos, and one with five or more photos, realistic MMK prices, and location-appropriate details. Include a long description and a known filter combination that produces no results.

## Web app routes

- `/` — home and search entry point
- `/search` — public search and filters
- `/listings/:id` — listing detail
- `/auth/sign-in` and `/auth/sign-up` — account access
- `/post` — create a listing
- `/dashboard` — listing management
- `/dashboard/listings/:id/edit` — edit an owned listing
- `/admin` — administrator workspace
- `*` — not-found page

## Mobile app scope

The mobile app supports home browsing, search results, filters in a bottom sheet, listing details and galleries, sign-in and sign-up, listing creation and management, and photo selection from the device. Initial delivery focuses on browsing, search, and details, followed by accounts, photo selection, and listing management.

## Accessibility and responsive behavior

- Keep core web flows usable from 360px upward and target WCAG 2.2 AA.
- Support keyboard navigation for navigation, dialogs, forms, filters, and galleries.
- Use visible labels, useful validation messages, meaningful image alt text (empty alt text for decorative images), semantic headings, and landmarks.
- Do not communicate status by color alone.

## Acceptance criteria

- Visitors can find listings in all three supported cities without signing in.
- Search filters affect results and are represented in shareable URLs.
- Unauthenticated users cannot create or modify listings, and users cannot modify another user's listing by changing an ID.
- A published listing has required contact details and at least one valid photo.
- Public search and listing URLs remain usable after refresh.
- The web experience works from desktop widths down to 360px, and a clean checkout can be started using project setup instructions.
