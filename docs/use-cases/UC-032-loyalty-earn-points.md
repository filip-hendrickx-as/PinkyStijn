# UC-032: Earn Loyalty Points

A registered customer who has joined the loyalty programme receives points when a purchase is paid, at the POS or in the webshop, building a balance they can later exchange for rewards (UC-034). This drives repeat visits and rewards customers for their spending with KRUZE.

## Metadata
- **Domain:** Customer / Loyalty / Sales
- **Primary Actor:** Registered Customer
- **Secondary Actors:** POS User / Cashier (POS purchases), Payment Provider (Mollie, webshop purchases)
- **Priority:** Could
- **Status:** Draft (MVP)
- **Interface:** Storefront, POS Terminal

## Scope (MVP)
- **In scope:** Opting in to the programme; earning points on paid POS and webshop purchases; viewing the points balance and points history in the existing storefront customer account (UC-026); the cashier seeing the customer's balance at the POS after looking the customer up (UC-021).
- **Out of scope:** A separate Loyalty App, push notifications, badges (UC-033), challenges and bonus points from challenges (UC-035), loyalty login/verification (UC-031), and refunds or reversals of sales (see below).
- **Refunds and reversals:** The application has no refund or reversal capability yet, so deducting points for a reversed sale is not part of the MVP. When a refund capability is introduced, the points deduction (including partial refunds and points already spent) needs to be specified at that time.
- **Configuration:** The earn rate is set by the admin in UC-043 (Configure Loyalty Programme).
- **Decided:** Enrolment is opt-in (BR-7). The earn rate is 1 point per EUR 1 spent, rounded down, configurable in UC-043 (BR-1). The amount counted includes VAT and excludes shipping (BR-2). Points are awarded at payment confirmation (BR-3).

## Preconditions
- The loyalty programme is configured (UC-043); the earn rate defaults to 1 point per EUR 1.
- The customer is a registered customer with a customer record (UC-002, UC-026) and has opted in to the loyalty programme (BR-7, AF-3).
- For a POS purchase: the cashier has selected the customer on the sale (UC-021 step 7). For a webshop purchase: the customer is logged in during checkout (UC-025).

## Main Flow
1. The registered customer logs in and places a webshop order through checkout (UC-025).
2. The system records the order and the customer pays through the payment provider.
3. The payment provider confirms the payment, and the system marks the order as paid.
4. The system identifies the customer on the order as a loyalty member (a customer who has opted in).
5. The system calculates the points earned: the order amount including VAT and excluding shipping, multiplied by the earn rate and rounded down to a whole number of points. Whether the amount is counted before or after a redeemed reward is undecided (see OQ-1).
6. The system adds the points to the customer's loyalty balance and records the points against the order.
7. The registered customer opens their account (UC-026) and asks for their loyalty overview.
8. The system shows the current points balance and the points history, listing for each entry: date, order or sale number, channel (POS or webshop), qualifying purchase amount, and points earned or spent.

## Alternative Flows
- **AF-1: Earn Points at the POS** — At step 1, instead of the webshop, the customer buys in the store. The cashier looks up the customer and selects them on the sale (UC-021 step 7). The cashier completes the sale and payment (UC-021 steps 12-14). The system then continues at step 4, treating the paid sale as the qualifying purchase. After the sale is recorded, the cashier can see the customer's updated points balance when looking up the customer. The flow continues at step 5.
- **AF-2: Bonus Points Promotion** — At step 5, a promotion awards extra points on top of the base calculation (e.g., double points weekends). *Whether promotions are part of the MVP is undecided; see OQ-6.* The flow continues at step 6.
- **AF-3: Opt In to the Loyalty Programme from the Account** — Before step 1, the registered customer opens their account (UC-026) and indicates they want to join the loyalty programme. The system asks the customer to confirm joining, records the opt-in with its date, and shows a zero points balance. The flow continues at step 1. Whether the customer can later opt out is undecided; see OQ-10.
- **AF-4: Opt In via the Order Confirmation Email** — At step 3, after the order is paid, the system sends the customer the existing order confirmation email (UC-025 step 15). If the customer is not a loyalty member, that email includes an invitation with a link to opt in to the loyalty programme. The customer follows the link, logs in if needed, and joins as in AF-3. The use case ends. Whether this purchase then earns points retroactively is undecided; see OQ-9.

## Exception Flows
- **EF-1: Customer Not a Loyalty Member** — At step 4 (webshop: guest checkout, or a customer who has not opted in) or at AF-1 (POS: no customer selected, the Kruze company customer remains assigned, or the selected customer has not opted in), the system cannot identify a loyalty member on the purchase. No points are awarded. Whether points can be claimed afterwards is undecided; see OQ-2, OQ-3, and OQ-9.
- **EF-2: Payment Not Completed** — At step 3, the payment fails or remains pending. No points are awarded until the payment is confirmed (BR-3).

## Postconditions
- The points earned are added to the customer's loyalty balance.
- The points history shows the transaction with the points earned.
- The cumulative balance is updated and visible in the customer's account and to the cashier at the POS.

## Business Rules
- **BR-1:** Points are earned at 1 point per EUR 1 spent, with the result rounded down to a whole number of points. The rate is configurable by the admin (UC-043); 1 is the default.
- **BR-2:** The amount that counts toward points includes VAT and excludes shipping cost.
- **BR-3:** Points are awarded on paid sales only, at the moment payment is confirmed; unpaid, pending, or failed orders earn nothing.
- **BR-4:** Points are awarded only to customers who are loyalty members (have opted in) at the time of the purchase.
- **BR-5:** Points are awarded at most once per paid sale or order.
- **BR-6:** Points earned feed the rewards system (UC-034). Badges (UC-033) are not part of the MVP.
- **BR-7:** Enrolment in the loyalty programme is opt-in. No customer is enrolled automatically, and a customer who has not opted in earns no points and cannot redeem rewards (UC-034).
- **BR-8:** The purchase-related email sent to a customer who is not a loyalty member must include an invitation with a link to opt in. Today the only such email is the webshop order confirmation (see Implementation Notes). The email sent to a customer who is already a member does not need the invitation.

## Open Questions
Decisions needed from the product owner. Nothing below has been decided; the flows above avoid assuming an answer. The questions were renumbered after the decisions on earn rate, timing, and refunds.

| ID | Question | Options |
|----|----------|---------|
| OQ-1 | Is the qualifying amount counted before or after a redeemed reward (UC-034) and after other discounts such as POS discounts (UC-021)? Linked to UC-034 OQ-3 | (a) Amount before any reward or discount; (b) amount after reward and discounts (price actually paid); (c) after rewards but before POS discounts, or the reverse |
| OQ-2 | Can walk-in (anonymous) POS customers join? | (a) No, a customer record is required; (b) yes, via quick-register at the POS (UC-021 / UC-002 AF-6); (c) yes, with only an email or phone number |
| OQ-3 | Can points be claimed for a purchase made before identification? | (a) No; (b) cashier can add the customer shortly after the sale; (c) customer claims via receipt code; (d) guest webshop orders are matched to a member by email |
| OQ-4 | Do points expire? | (a) Never; (b) fixed period after earning; (c) after a period of inactivity; and if yes, is the customer warned (email is the only channel as push is out of scope) |
| OQ-5 | Does customer-specific pricing (UC-007) affect earning? | (a) Points on the price actually paid; (b) customers with customer-specific prices earn no points; (c) different rate for them |
| OQ-6 | Are bonus-point promotions (e.g., double-points weekends) in the MVP? | (a) No, future; (b) yes, admin-configurable multiplier for a date range; (c) per product or product type |
| OQ-7 | Do B2B customers earn points? | (a) No, B2C only; (b) yes, same as B2C; (c) per-customer flag |
| OQ-8 | Should a points balance be adjustable manually by the admin (corrections, goodwill)? | (a) No, not in MVP; (b) yes, with a reason recorded |
| OQ-9 | Does opting in grant points retroactively for the purchase that triggered the opt-in (AF-4)? | (a) No, only purchases after opting in; (b) yes for the triggering order if opted in within a time window; (c) yes for all past orders |
| OQ-10 | Can a customer opt out, and what happens to the balance? | (a) No opt-out in the MVP; (b) opt-out with the balance forfeited; (c) opt-out with the balance kept and frozen; (d) opt-out with the balance and history erased (privacy request) |
| OQ-11 | Can the cashier opt a customer in at the POS? | (a) No, only the customer in the account or via the email link; (b) yes, with the customer's consent confirmed verbally; (c) yes, with confirmation by email. Relates to OQ-2 |
| OQ-12 | What consent wording and record is needed for opting in? | Plain confirmation; terms and privacy text shown with a recorded date; double opt-in by email |
| OQ-13 | Should customers who have not opted in be invited in other communications, such as the invoice email or the POS receipt? | (a) Only the webshop order confirmation (BR-8); (b) also the invoice email; (c) also the POS receipt (a printed link or code) |

## Implementation Notes
- Purchase-related emails that exist today (verified in the code): (1) the webshop order confirmation to the customer, sent after successful payment when the order has an email address (`server/checkoutService.ts`, `sendCustomerConfirmation`, via Resend); (2) an internal new-order notification to the admin (`sendAdminNotification`), which is not customer-facing and needs no invitation; (3) the invoice email with PDF attached, which an admin triggers manually for a sale that has an invoice number (route in `server/routes.ts`; see UC-008). No payment-failure email exists, and the POS sends no emailed receipt. The webshop order confirmation is therefore the only customer-facing email that is sent automatically after a purchase.
- The order confirmation needs to know whether the customer is a loyalty member, matching the order to a customer record, which is not guaranteed for guest checkout.
- A loyalty table already exists in the data model (customer reference, points, and deductions), but no routes, services, or components use it. It needs review against the decisions above (points history per transaction, expiry, opt-in status and date) before being relied upon.
- Points calculation must run when a POS sale is recorded (UC-021 step 14) and when the webshop payment confirmation is processed (UC-025 step 12), and must be idempotent because the payment provider can notify more than once.
- The account overview (UC-026) and the POS customer lookup (UC-021) need to expose the balance; no additional interface is introduced.

## Implemented By
- Status: **Not yet implemented** — planned MVP feature. A `loyalty` table exists in the schema but is not used by any route, service, or component.
