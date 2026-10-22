HexCommerce BRD v3.0 · 22 October 2026 · Strategic domain-driven design

# HexCommerce Context Map

The BRD's 31 processes, 317 functional requirements and 111 business rules resolve into **20 bounded contexts**: five core, ten supporting and five generic, with four external systems. Each boundary follows a change in the language, an owner or a consistency rule in the document.

[Context map](#map)[Bounded contexts](#contexts)[Relationship patterns](#patterns)[Relationship register](#relationships)[Where the language splits](#language)[Design notes](#notes)

5CoreWhere HexCommerce competes: discovery, deals, checkout, ordering and seller supply.

10SupportingSpecific to the business but not what makes it win.

5GenericProblems every business has; buy or adopt and conform.

4ExternalPayment provider, email service, carriers, and a tax engine if needed.

## Context map

Select a context to trace its relationships, or pick a pattern to see where it is used. Arrows run from upstream to downstream.

Highlight

Scroll sideways to see the whole map.

Twenty bounded contexts of HexCommerce coloured by type, with four external systems in dashed outline. Arrows point from the upstream context to the downstream one. The platform band relates to every context and is not drawn edge by edge; a few secondary links appear only in the register.

### Reading the map

Upstream and downstream describe whose model shapes whose, which is not always the direction data travels: Checkout sends claims to Deals & Offer Pricing, yet Deals is upstream because Checkout must work with the deal model as Deals defines it. The two partnerships and the shared kernel are the places where teams must plan changes together.

## Bounded contexts

Each context below owns its model and its words. "Owns" lists the aggregates and rules that only it may change, with the BRD identifiers they come from.

### Core 5

Where HexCommerce competes. Build in-house, model richly, put the strongest people here.

#### Search & Discovery

Core

Help shoppers get from a need to the right item quickly, across the store's catalogue and every approved seller's listings (BO-01).

Owns

- Search index of sellable items: catalogue products and listings on sale
- Keyword matching and relevance ranking (FR-CAT-005 to 007); suggestions after two characters (FR-CAT-008)
- Filters with brand counts, six sort orders, the featured order and price bands (FR-CAT-012 to 021)
- Popularity score (BR-CAT-001), related products (FR-PDP-011), shareable result sets (FR-CAT-026), recently viewed (FR-ACC-012)

Language

relevance, facet, price band, featured order, popularity score, newest arrivals

Why core

Finding things fast across first- and third-party supply is how the marketplace wins visits, and the ranking rules are HexCommerce's own. Read model built from other contexts' published events; it changes nothing upstream.

From the BRD

CAT · PDPP01

#### Deals & Offer Pricing

Core

Decide what each item sells for right now and how many units are offered at that price.

Owns

- SellerDeal SD-NNNNN: Scheduled, Live, Ended, derived from its dates (BR-SDL-007)
- StoreDeal LD-NNNNN: Scheduled, Running, Ended, by date, time and units left (BR-STD-005)
- Deal price, discount percentage and claimed units (BR-PRC-001, BR-SDL-006, BR-SDL-009, BR-STD-004)
- Units on offer: the lower of stock and deal units left (BR-SDL-014, BR-CRT-002); 5-70% and no-overlap rules

Language

deal units, claimed, units on offer, deal price, was price, paused, start early

Why core

Deals and savings raise basket value (BO-03), and the claim-counting and honest-deal rules (BO-07) are specific to this marketplace. One model with two owners: sellers run seller deals, merchandisers run store deals. The rules are nearly identical, so one context keeps the claim logic that checkout depends on in one place.

From the BRD

SDL · MKT · PRCP10, P17, P29

#### Checkout

Core

Turn a basket into a paid order without losing the sale or overselling (BO-02).

Owns

- CheckoutSession: delivery address, card, delivery option, items and totals
- Placement checks: units available (FR-CHK-008), card not expired (BR-ACC-010), not the shopper's own listing (FR-CHK-023), signed in (FR-CHK-021)
- Order totals: subtotal, delivery fee, tax, total (BR-PRC-002 to 007)
- The place-order saga: reserve, authorise, create, all or nothing (FR-CHK-022, FR-PAY-002)

Language

checkout, delivery option, order total, authorisation, buy now

Why core

Conversion is a business objective in its own right, and the all-or-nothing rule protects sellers and stock (R-01). Process manager. It holds no long-lived state once the order exists.

From the BRD

CHKP03, and P02 for buy now

#### Ordering

Core

Hold the shopper's order of record from placement to delivery, cancellation and refund.

Owns

- Order 204-NNNNNNN-NNNNNNN with its lines, and the snapshot taken at placement (BR-ORD-003)
- Line status Processing, Shipped, Out for delivery, Delivered or Cancelled; order status derived from the lines (BR-ORD-008)
- Cancellation before dispatch (BR-ORD-009, BR-ORD-011) and the refund due (BR-PRC-009)
- Invoices (FR-ORD-008) and replacement lines at no charge (FR-ORD-023)

Language

order, line, Processing, Shipped, dispatching party, invoice, refund due

Why core

This is where one shopper's purchase from many sellers becomes one coherent order. Line-level status (Q-07) answers the 'where is my order' contacts behind R-06. Owner of order status; other contexts report events to it.

From the BRD

ORDP04, P23, P24

#### Seller Listings

Core

Put approved sellers' products on sale alongside the store's own (BO-05, BO-06).

Owns

- Listing SL-NNNNN: Draft, Active, Out of stock or Unpublished (BR-SLS-002, BR-SLS-003)
- Units in stock, selling and was price, condition, up to six images (BR-SLS-001, 004, 006)
- On sale means Active and the seller approved (FR-PDP-014, BR-SAP-004)
- Stock reduced on sale, never below 0, Out of stock at 0 (BR-SLS-005)

Language

listing, draft, publish, unpublish, condition, cover image, needs attention

Why core

Third-party supply is the marketplace's growth engine. Listing status and stock form one invariant, so they stay in one aggregate. Publishes listing changes to every storefront context.

From the BRD

SLSP09, P21

### Supporting 10

Specific to HexCommerce but not what makes it win. Build simply, or customise something off the shelf.

#### Store Catalogue

Supporting

Keep the store's own products, prices, stock and department structure current.

Owns

- CatalogueProduct: Unpublished, Published or Retired; price, list price, stock, features, specifications, options, keywords
- First-published date for newest arrivals (FR-CTL-006, BR-CAT-004)
- Department and sub-department taxonomy (FR-CAT-001, FR-CAT-002, FR-CTL-005)
- Publication rules (BR-CTL-001) and the bulk initial load (FR-DAT-012)

Language

catalogue product, publish, retire, list price, department

Why supporting

Necessary and specific to the store, but conventional product-information management. Owned by the catalogue team (R9). Upstream of the taxonomy everyone uses.

From the BRD

CTL · CATP30

#### Merchandising

Supporting

Shape what shoppers see first: promotions, featured selections and badges.

Owns

- Promotion: headline, description, destination, schedule and order (FR-MKT-001, FR-MKT-015)
- FeaturedSelection: chosen products or a rule, and the number shown, such as Best sellers and Biggest savings (FR-MKT-006, 007, 019)
- Badge assignments: Best Seller, Deal of the Day, Hex Choice (FR-MKT-014, FR-MKT-020)

Language

promotion, destination, featured selection, badge

Why supporting

Scheduled content with light rules and no transactional invariants. Owned by the merchandising team (R8).

From the BRD

MKTP29

#### Shopping Basket & Lists

Supporting

Collect items to buy now or later, within the quantity limits.

Owns

- Basket with the 10-unit limit (BR-CRT-001) and flagged lines above units available (BR-CRT-004)
- Saved for later and wish list, each item held once, newest first (BR-CRT-007)
- Guest basket kept for the visit and merged on sign-in (FR-CRT-018)
- Basket summary and free-delivery progress (FR-CRT-009, FR-CRT-010)

Language

basket line, limit reached, saved for later, wish list, free-delivery progress

Why supporting

Standard e-commerce behaviour carrying HexCommerce's limits. Shares its totals model with Checkout.

From the BRD

CRTP02, P19

#### Customer Account

Supporting

Keep the shopper's addresses and cards ready for checkout, and honour their data-protection rights.

Owns

- Customer profile: name, email, mobile, member-since date (FR-ACC-001, FR-ACC-023)
- Address book: exactly one default, never the last removed, a selected delivery address (BR-ACC-001 to 004)
- Card wallet of provider references, brand, last four and expiry (BR-ACC-005 to 008, BR-ACC-010)
- Account closure and personal-data export (FR-DAT-010, FR-DAT-011)

Language

account book, default address, selected delivery address, default card, expired

Why supporting

Needed by checkout; its rules are local and stable. Never stores a card number; Payments returns a reference.

From the BRD

ACC · DATP07, P16

#### Fulfilment & Delivery

Supporting

Get every order line from dispatch to the doorstep, whoever dispatches it.

Owns

- SellerOrder SO-NNNNN: New, Dispatched, Delivered or Cancelled; buyer name and city only (BR-SOR-001, BR-SOR-005)
- Store-item dispatch by the fulfilment team, with carrier and tracking reference (FR-ORD-015)
- Shipment and its carrier events: out for delivery, delivered, failed attempt (FR-ORD-016)
- The delivery promise: next-day cut-off, then +1, +2 or +4 days (BR-ORD-002)

Language

dispatching party, seller order, dispatch, tracking reference, out for delivery, failed attempt

Why supporting

Essential operations, but the marketplace holds no seller stock (FR-DEF-013) and does no warehouse management (section 3.4). Two dispatching parties, one model: the fulfilment team for store items, the seller for marketplace items.

From the BRD

SOR · ORDP11, P23

#### Returns

Supporting

Take back delivered items within the returns policy and refund or replace them.

Owns

- ReturnRequest RT-NNNNN: Requested, Awaiting items, Received, Accepted or Rejected, Completed (BR-ORD-012)
- Eligibility: Delivered and inside the returns window (BR-ORD-006)
- Receipt, inspection and decision by the dispatching party (FR-ORD-022, FR-SOR-007)
- The shopper's choice of refund or replacement (FR-ORD-020)

Language

return reason, awaiting items, inspection, refund or replacement

Why supporting

A policy-driven workflow. Who handles returns of seller items is still open (Q-39). Asks Ordering for the refund or replacement; never moves money itself.

From the BRD

ORD · SORP25

#### Customer Service

Supporting

Resolve shoppers' problems with ordered items within the 24-hour promise (BO-04).

Owns

- SupportIssue CS-NNNNN: Open, In review, Awaiting your reply, Resolved (BR-CS-001)
- Message thread with internal notes never shown to the shopper (BR-CS-005)
- Agent queue, assignment, unread flag and the 24-hour flag (FR-CS-015 to 020, BR-CS-003)
- Nine support topics and help information (FR-CS-002, FR-CS-014)

Language

issue, topic, thread, internal note, awaiting your reply, reopen

Why supporting

Matters for trust but behaves like any helpdesk. If a helpdesk product is bought, this becomes generic behind an ACL. Starts cancellations and returns on the shopper's behalf through Ordering and Returns (FR-CS-021).

From the BRD

CSP05, P15, P22

#### Ratings & Reviews

Supporting

Trustworthy customer opinion of catalogue products (BO-07).

Owns

- Review: rating, headline, text; published or removed; Verified Purchase; edited date (BR-REV-001 to 003)
- Helpful votes, one per shopper per review, never on one's own (BR-REV-005)
- Review reports and moderation decisions by support agents (BR-REV-007, FR-REV-014)
- Rating summary: average, count, per-star distribution (BR-REV-004, BR-REV-006)

Language

verified purchase, helpful vote, report reason, moderation, rating distribution

Why supporting

Builds trust with conventional mechanics. Covers catalogue products only (Q-13).

From the BRD

REVP06, P18, P31

#### Seller Management

Supporting

Vet businesses that apply to sell, and hold the business record of approved sellers.

Owns

- SellerApplication SA-NNNNN: Pending, Approved or Rejected; a decided application can return to Pending (BR-SAP-003)
- Reapplication as a new application, with history kept (FR-SAP-015)
- SellerProfile: business details, Verified status, seller-since date (BR-SAP-005, BR-SAP-006, FR-SDB-005 to 007)
- The seller-tools gate (FR-SAP-010) and the conflict-of-interest check (FR-ADM-015)

Language

applicant, application, approve, reject, return to Pending, Verified, seller since

Why supporting

Vetting protects the marketplace (BO-05), but it is a controlled workflow rather than a differentiator. Its approval decision is broadcast to every seller-facing context.

From the BRD

SAP · ADM · SDBP08, P13, P14

#### Seller Payouts

Supporting

Pay sellers what they are owed for delivered sales, net of referral fees and returns.

Owns

- PayoutAccount: Awaiting verification, Verified or Rejected; masked; changes need re-authentication (FR-SPY-001 to 003)
- Referral-fee schedule by department, 12% by default (BR-SPY-001, FR-SPY-004)
- Payout statements and payout runs approved by finance; payouts held without a verified account (BR-SPY-002, BR-SPY-003)
- Seller earnings: revenue, units sold, estimated payout (BR-SOR-002 to 004)

Language

payout, referral fee, payout statement, payout run, held, verified account

Why supporting

The take rate matters commercially, but the rules are finance-controlled bookkeeping. Owned with finance (R10).

From the BRD

SPYP27, P28

### Generic 5

Problems every business has. Buy or adopt, and conform to the product's model.

#### Identity & Access

Generic

Prove who someone is and what they may do.

Owns

- Customer registration, sign-in by email or mobile, lock-out, reset links, sign-out and inactivity timeout (BR-ACC-011 to 013, FR-ACC-020)
- Staff users with roles and multi-factor sign-in (FR-ADM-011, FR-ADM-012)
- Role check for every back-office capability (FR-ADM-010)

Language

session, sign in, lock-out, staff user, role

Why generic

A solved problem: adopt an identity provider and conform to it. Upstream of every context.

From the BRD

ACC · ADMP12, P20, P26

#### Payments

Generic

Move money through the payment service provider and record every movement.

Owns

- Card safekeeping references (FR-PAY-001)
- Authorisation, capture per line on dispatch, release and refund (FR-PAY-002 to 005)
- Seller payout transfers (FR-SPY-006)
- Payment transaction ledger: type, amount, date, outcome, provider reference (FR-PAY-006)

Language

authorisation, capture, release, refund, provider reference

Why generic

The provider does the hard part. This context is the anti-corruption layer that keeps card data out of the marketplace (NFR-SEC-010). Gateway context.

From the BRD

PAYP03, P07, P23 to P25, P28

#### Notifications

Generic

Tell shoppers, sellers and applicants by email what they need to know.

Owns

- Notification types, recipients and send log (FR-NTF-001 to 010)
- Content restrictions: no full card numbers, passwords or buyer contact details beyond name and city (FR-NTF-011)

Language

notification, recipient

Why generic

Subscribes to other contexts' events; the email service sits behind an ACL. Downstream of every context's published events.

From the BRD

NTFMost processes

#### Audit

Generic

Keep a tamper-evident record of every back-office change.

Owns

- Audit record: who, what, when, values before and after (FR-ADM-013)
- Search and export (FR-ADM-014); retention of at least 7 years (NFR-SEC-012)

Language

audit record

Why generic

An append-only log fed by events from every back-office context. Downstream of every back-office context.

From the BRD

ADMP26 and every back-office process

#### Store Configuration

Generic

Let the store operator re-brand and re-parameterise the store without development work (BO-08).

Owns

- Brand, currency, locale and time zone (FR-CFG-001, 002, 005)
- Free-delivery threshold 35.00, delivery fee 5.99, tax rate 8.75%, next-day cut-off 23:00, returns window 30 days (FR-CFG-003, 004, 007 to 009)
- Fallback to defaults (FR-CFG-006) and the business date (BR-SYS-001)

Language

setting, default, business date

Why generic

A settings service. The rules that use these values stay in their own contexts. Upstream of every context.

From the BRD

CFGAll

### External systems 4

Outside the marketplace. Each is reached through an anti-corruption layer in the context that uses it.

- **Payment service provider** Card safekeeping, authorisation, capture, release, refunds and seller payouts (section 11.1).
- **Carriers** Report delivery progress against tracking references (FR-ORD-016).
- **Email service** Delivers notification emails (FR-NTF-001 to 011).
- **Tax engine, if needed** Tax calculation if rates vary by region or product; depends on Q-03.

## Relationship patterns

The integration patterns used on the map, from tightest coupling to none.

OHS/PL ▸ CF

**Open host service, published language ▸ conformist.** The upstream publishes a stable protocol or event language for many consumers; the downstream adopts it as is.

OHS ▸ CF

**Open host service ▸ conformist.** A shared service API that consumers adopt as offered.

C/S

**Customer–supplier.** The upstream (supplier) plans for what the downstream (customer) needs; both teams negotiate the interface.

Partnership

**Partnership.** Two contexts succeed or fail together, so their teams plan and release changes jointly.

Shared kernel

**Shared kernel.** A small model owned jointly by two teams; neither changes it alone.

ACL

**Anti-corruption layer.** The downstream translates an external model into its own terms so the outside vocabulary stops at the boundary.

Separate ways

**Separate ways.** No integration, by decision.

## Relationship register

All 45 relationships with what crosses each boundary. Rows marked "platform band" or "register only" are not drawn as separate lines on the map.

| ID | Upstream → downstream | Pattern | What crosses the boundary | BRD |
| --- | --- | --- | --- | --- |
| R01 | Store Catalogue → Seller Listings | OHS/PL ▸ CF | Department and sub-department taxonomy that listings are classified by. | FR-SLS-004, FR-SLS-005, FR-CTL-005 |
| R02 | Seller Management → Seller Listings | OHS/PL ▸ CF | Approval puts a seller's listings on sale; rejection or return to Pending withdraws them. Business-name changes follow. | FR-ADM-008, FR-ADM-009, BR-SAP-004, FR-SDB-007 |
| R03 | Store Catalogue → Deals & Offer Pricing | OHS/PL ▸ CF | Product price, list price and units in stock for store deals. | BR-STD-002, BR-STD-004 |
| R04 | Seller Listings → Deals & Offer Pricing | OHS/PL ▸ CF | Listing price, stock and on-sale state; removing a listing removes its deals. | BR-SDL-002, BR-SDL-008, BR-SDL-012, BR-SDL-014, BR-SLS-007 |
| R05 | Store Catalogue → Merchandising | OHS/PL ▸ CF | Catalogue products that can carry badges or be featured. | FR-MKT-014, FR-MKT-019, FR-MKT-020 |
| R06 | Store Catalogue → Search & Discovery | OHS/PL ▸ CF | Published products with searchable text, department and first-published date. | FR-CAT-005, FR-CTL-006, BR-CAT-004 |
| R07 | Seller Listings → Search & Discovery | OHS/PL ▸ CF | Listings on sale, with seller name and condition. | FR-SLS-020, FR-CAT-027, FR-CAT-034, FR-PDP-012 |
| R08 | Deals & Offer Pricing → Search & Discovery | OHS/PL ▸ CF | Current price, discount, deal status, time remaining and share claimed. | FR-CAT-031, FR-MKT-005, FR-MKT-010, FR-MKT-012, FR-PDP-013 |
| R09 | Merchandising → Search & Discovery | OHS/PL ▸ CF | Badges for the featured order, promotions and featured selections. | FR-CAT-020, FR-MKT-001, FR-MKT-006, FR-MKT-007 |
| R10 | Ratings & Reviews → Search & Discovery | OHS/PL ▸ CF | Average rating and rating count for the rating filter, sorts and popularity score. | FR-CAT-016, FR-CAT-030, BR-CAT-001 |
| R11 | Deals & Offer Pricing → Shopping Basket & Lists | C/S | Line price and units available: stock, capped by deal units left. | BR-CRT-002, FR-CRT-008 |
| R12 | Shopping Basket & Lists ⇄ Checkout | Shared kernel | One jointly owned model of totals and limits: delivery fee and threshold, tax, rounding, the units-available check. Checkout reads the basket and empties it on placement. | BR-PRC-002 to 006, BR-CRT-005, FR-CRT-009, FR-CHK-013 |
| R13 | Customer Account → Checkout | C/S | Selected delivery address, default card and cards not expired; either can be added during checkout. | FR-CHK-002, FR-CHK-003, FR-CHK-016, BR-ACC-010 |
| R14 | Deals & Offer Pricing → Checkout | C/S | Deal price at placement, and claiming deal units. | FR-CHK-012, BR-SDL-009, BR-STD-004 |
| R15 | Store Catalogue → Checkout | C/S | Reserve and reduce product stock at placement; restore it for cancellations and accepted returns. | FR-CHK-011, BR-ORD-011, FR-ORD-023 |
| R16 | Seller Listings → Checkout | C/S | Reserve and reduce listing stock (Out of stock at zero); the listing's seller for the own-listing check. | FR-CHK-011, BR-SLS-005, FR-CHK-023 |
| R17 | Checkout ⇄ Ordering | Partnership | The order exists only when authorisation, stock and deal claims all succeed; prices, address, card and totals are captured at placement. | FR-CHK-022, FR-PAY-002, FR-CHK-019, FR-CHK-020, BR-ORD-003 |
| R18 | Ordering ⇄ Fulfilment & Delivery | Partnership | A placed order creates seller orders and store dispatch work; dispatch, carrier and delivery events move order lines; cancellations flow both ways. | FR-CHK-010, FR-ORD-013, FR-ORD-014, FR-ORD-019, FR-SOR-006 |
| R19 | Fulfilment & Delivery → Checkout | OHS ▸ CF | Delivery options with expected dates: next-day cut-off, then +1, +2 or +4 days. | BR-ORD-002, FR-CHK-005, FR-CHK-018 |
| R20 | Payments → Checkout | OHS ▸ CF | Authorise the order total; a decline gives no issuer reason. | FR-PAY-002, FR-PAY-003 |
| R21 | Payments → Ordering | OHS ▸ CF | Capture per line on dispatch, release on cancellation, refunds; every movement recorded against the order. | FR-PAY-004, FR-PAY-005, FR-PAY-006 |
| R22 | Payments → Customer Account | OHS ▸ CF | Card safekeeping: only the provider reference, brand, last four and expiry come back. | FR-PAY-001, BR-ACC-008 |
| R23 | Payments → Seller Payouts | OHS ▸ CF | Pay approved payouts into verified accounts, with payment date and reference. | FR-SPY-006 |
| R24 | Ordering → Returns | C/S | Eligibility (Delivered, inside the window, who dispatched); refund of the amount paid, or a replacement line. | BR-ORD-006, BR-PRC-009, FR-ORD-023 |
| R25 | Ordering → Customer Service | C/S | Items eligible for an issue; the order with its payment and fulfilment history for agents; cancelling on the shopper's behalf. | BR-ORD-007, FR-CS-001, FR-CS-017, FR-CS-021 |
| R26 | Returns → Customer Service | C/S | Starting a return from an issue; return history for agents. | FR-CS-021, FR-CS-017 |
| R27 | Ordering → Ratings & Reviews | OHS/PL ▸ CF | Purchases that earn the Verified Purchase label; cancelled lines do not count. | FR-REV-006, BR-ORD-007 |
| R28 | Fulfilment & Delivery → Seller Payouts | OHS/PL ▸ CF | Seller orders delivered or cancelled, with delivery dates. | BR-SOR-002, BR-SOR-003, BR-SPY-002 |
| R29 | Returns → Seller Payouts | OHS/PL ▸ CF | Accepted returns of seller items, deducted from revenue and payout. | BR-SOR-002, BR-SPY-002 |
| R30 | Seller Management → Seller Payouts | OHS/PL ▸ CF | An approved seller becomes a payee; the business name pre-fills the account holder. | FR-SPY-001, BR-SAP-005 |
| R31 | Payment service provider → Payments | ACL | Provider API for card safekeeping, payments, refunds and payouts, translated into the marketplace's terms. | FR-PAY-001 to 006, NFR-SEC-010 |
| R32 | Carriers → Fulfilment & Delivery | ACL | Carrier delivery events, translated into out for delivery, delivered and failed attempt. | FR-ORD-016 |
| R33 | Email service → Notifications | ACL | Email provider API behind an adapter, so the provider can change. | FR-NTF-001 to 011 |
| R34 | Tax engine, if needed → Checkout | ACL | Tax calculation, only if rates vary by region or product. | BR-PRC-003, Q-03 |
| R35 | Identity & Access → Every contextplatform band | OHS ▸ CF | Who is signed in, staff roles, sessions. Sign-in also merges the guest basket and registration creates the customer account. | FR-ADM-010, FR-ACC-022, FR-CRT-018 |
| R36 | Store Configuration → Every contextplatform band | OHS/PL ▸ CF | Delivery fee and threshold, tax rate, cut-off, returns window, currency, locale and business date, read by Basket, Checkout, Fulfilment, Returns, Payouts and Discovery. | FR-CFG-001 to 009, BR-SYS-001 |
| R37 | Every context → Notificationsplatform band | OHS/PL ▸ CF | Published domain events that trigger emails: order placed, dispatched, cancelled, return outcome, issue reply, application decision, payout paid. | FR-NTF-001 to 010 |
| R38 | Every context → Auditplatform band | OHS/PL ▸ CF | Every back-office change: decisions, refunds, moderation, payout approvals, roles, configuration. | FR-ADM-013 |
| R39 | Seller Management → Deals & Offer Pricingregister only | OHS ▸ CF | Seller-tools gate: only approved sellers manage deals. | FR-SAP-010, FR-SAP-011 |
| R40 | Seller Management → Fulfilment & Deliveryregister only | OHS ▸ CF | Seller-tools gate: only approved sellers work seller orders. | FR-SAP-010, FR-SAP-011 |
| R41 | Fulfilment & Delivery → Shopping Basket & Listsregister only | OHS ▸ CF | Delivery promise for each basket line's estimated arrival. | FR-CRT-002, BR-ORD-002 |
| R42 | Fulfilment & Delivery → Search & Discoveryregister only | OHS ▸ CF | Delivery promise for results and item details. | FR-CAT-033, FR-PDP-007 |
| R43 | Customer Account → Search & Discoveryregister only | C/S | City and postcode of the selected delivery address, wherever delivery estimates are given. | FR-ACC-007 |
| R44 | Store Catalogue → Seller Payoutsregister only | OHS/PL ▸ CF | Departments that the referral-fee schedule is set by. | FR-SPY-004, BR-SPY-001 |
| R45 | Ratings & Reviews ✕ Seller Listingsregister only | Separate ways | No link by decision: reviews cover catalogue products only and listings carry no rating. | Q-13, BR-SLS-008 |

## Where the language splits

The same word means different things on either side of a boundary. These shifts are the main evidence for where the boundaries sit.

| Term | Meaning by context |
| --- | --- |
| Item on sale | Store Catalogue a catalogue product, Published  Seller Listings a listing, Active, from an approved seller  Search & Discovery a result of either kind  Ordering a line whose title and price were captured at placement |
| Order | Ordering the shopper's order 204-NNNNNNN-NNNNNNN, status derived from its lines  Fulfilment & Delivery a seller order SO-NNNNN for one marketplace item, showing only buyer name and city |
| Deal | Deals & Offer Pricing a seller deal SD-NNNNN runs by date and is Live; a store deal LD-NNNNN runs by date and time and is Running |
| Stock | Store Catalogue, Seller Listings units in stock  Deals, Basket, Checkout units available: stock capped by deal units left  Search & Discovery stock status: unavailable at 0, low at 1 to 10, in stock above 10 |
| Seller | Seller Management an applicant, then a Verified seller  Fulfilment, Returns a dispatching party  Seller Payouts a payee with a payout account  Search & Discovery the 'sold by' name |
| Account | Identity & Access credentials, sessions, staff users  Customer Account the account book of addresses and cards  Seller Payouts a bank account to be paid into |
| Delivered | Ordering a line status  Fulfilment & Delivery a seller-order status  Returns starts the returns window  Seller Payouts starts the hold before a sale is paid out |
| Refund | Ordering the amount due (BR-PRC-009)  Payments a money movement through the provider  Seller Payouts a deduction from the seller's payout |

## Design notes

Consequences of these boundaries for the solution design that follows the BRD.

#### Order placement crosses five contexts

FR-CHK-022 wants the order, its seller orders, the stock reductions and the deal claims recorded together or not at all. Across contexts, run it as a saga owned by Checkout: reserve stock in Store Catalogue and Seller Listings and deal units in Deals, authorise through Payments, then let Ordering commit the order and publish OrderPlaced through a transactional outbox, from which Fulfilment creates the seller orders. Any failure releases the reservations and voids the authorisation. Seller orders appear a moment after the order but cannot be lost, which is the risk R-01 describes. Confirm this reading with Engineering, because Q-09 was closed on the word "together".

#### Ordering owns the truth about status

Fulfilment & Delivery reports dispatch and carrier events; Ordering derives the order status from its lines (BR-ORD-008). The carriers' vocabulary stops at the anti-corruption layer, and seller orders never write to the shopper's order directly.

#### Seller approval is a broadcast

Approving, rejecting or re-queuing an application changes what Seller Listings, Deals, Fulfilment and Payouts allow. FR-ADM-008 and FR-ADM-009 say the effect is immediate, so seller-facing contexts should check the seller-tools gate on every action, while the storefront catches up through the published event.

#### Compositions are not contexts

The product detail view, the seller account overview (FR-SDB-001 to 004), the agent's issue view (FR-CS-017) and the order details each gather data from several contexts. Build them as compositions owned by the face that shows them. The storefront, seller tools and back office in Figure 1 are channels, not bounded contexts.

#### Why there is no Inventory context

A listing's status and its stock form one invariant (BR-SLS-002, BR-SLS-005), and warehouse management is out of scope (section 3.4), so stock stays with its owner. Revisit this if stock per colour or size arrives (Q-11, FR-DEF-008), or fulfilment by the marketplace (FR-DEF-013), which would also split Fulfilment into store and marketplace fulfilment.

#### Open questions that could move a boundary

Q-13: reviews of listings would turn Separate ways into a link from Seller Listings to Ratings & Reviews. Q-39: if the marketplace receives seller returns, Returns gains a store-side handling flow. Q-03: a tax engine would sit behind an ACL in Checkout. Q-37: moderation could move from Ratings & Reviews to Customer Service.

Source: HexCommerce Business Requirements Document v3.0 (Warren J, 22 October 2026), sections 4 to 12: user roles, capability map, status lifecycles, processes P01 to P31, functional modules, business rules, entities and integration points. Classification follows the business objectives BO-01 to BO-11.