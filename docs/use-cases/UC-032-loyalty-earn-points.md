# UC-032: Earn Loyalty Points

A registered customer automatically receives loyalty points when a purchase is paid, at the POS or in the webshop, building a balance they can later exchange for rewards (UC-034). This drives repeat visits and rewards customers for their spending with KRUZE.

## Metadata
- **Domain:** Customer / Loyalty / Sales
- **Primary Actor:** Registered Customer
- **Secondary Actors:** POS User / Cashier (POS purchases), Payment Provider (Mollie, webshop purchases)
- **Priority:** Could
- **Status:** Draft (MVP)
- **Interface:** Storefront, POS Terminal

## Scope (MVP)
- **In scope:** Earning points on paid POS and webshop purchases; viewing the points balance and points history in the existing storefront customer account (UC-026); the cashier seeing the customer's balance at the POS after looking the customer up (UC-021).
- **Out of scope:** A separate Loyalty App, push notifications, badges (UC-033), challenges and bonus points from challenges (UC-035), and loyalty login/verification (UC-031). These are not part of the MVP.
- **Related capability (not part of this UC):** The admin configures the earn rate and the rewards catalogue. See Preconditions and Open Question OQ-12.
- **Decided:** Enrolment is opt-in (BR-7). Previously OQ-3.

## Preconditions
- The earn rate has been configured by the admin (see OQ-12 for where this is configured).
- The customer is a registered customer with a customer record (UC-002, UC-026) and has opted in to the loyalty programme (enrolment is opt-in, see BR-7 and AF-3).
- For a POS purchase: the cashier has selected the customer on the sale (UC-021 step 7). For a webshop purchase: the customer is logged in during checkout (UC-025).
- A purchase is completed and paid.

## Main Flow
1. The registered customer logs in and places a webshop order through checkout (UC-025).
2. The system records the order and the customer pays through the payment provider.
3. The payment provider confirms the payment, and the system marks the order as paid.
4. The system identifies the customer on the order as a loyalty member (a customer who has opted in).
5. The system calculates the points earned from the qualifying purchase amount and the configured earn rate (see OQ-1 and OQ-2).
6. The system adds the points to the customer's loyalty balance and records the points against the order.
7. The registered customer opens their account (UC-026) and asks for their loyalty overview.
8. The system shows the current points balance and the points history, listing for each entry: date, order or sale number, channel (POS or webshop), purchase amount, and points earned or deducted.

## Alternative Flows
- **AF-1: Earn Points at the POS** — At step 1, instead of the webshop, the customer buys in the store. The cashier looks up the customer and selects them on the sale (UC-021 step 7). The cashier completes the sale and payment (UC-021 steps 12-14). The system then continues at step 4, treating the paid sale as the qualifying purchase. After the sale is recorded, the cashier can see the customer's updated points balance when looking up the customer. The flow continues at step 5.
- **AF-2: Bonus Points Promotion** — At step 5, a promotion awards extra points on top of the base calculation (e.g., double points weekends). *Whether promotions are part of the MVP is undecided; see OQ-10.* The flow continues at step 6.
- **AF-3: Opt In to the Loyalty Programme from the Account** — Before step 1, the registered customer opens their account (UC-026) and indicates they want to join the loyalty programme. The system asks the customer to confirm joining, records the opt-in with its date, and shows a zero points balance. The flow continues at step 1. Whether the customer can later opt out is undecided; see OQ-16.
- **AF-4: Opt In via the Order Confirmation Email** — At step 3, after the order is paid, the system sends the customer the existing order confirmation email (UC-025 step 15). If the customer is not a loyalty member, that email includes an invitation with a link to opt in to the loyalty programme. The customer follows the link, logs in if needed, and joins as in AF-3. The use case ends. Whether this purchase then earns points retroactively is undecided; see OQ-15.

## Exception Flows
- **EF-1: Customer Not a Loyalty Member** — At step 4 (webshop: guest checkout, or a customer who has not opted in) or at AF-1 (POS: no customer selected, the Kruze company customer remains assigned, or the selected customer has not opted in), the system cannot identify a loyalty member on the purchase. No points are awarded. Whether points can be claimed afterwards is undecided; see OQ-4, OQ-5, and OQ-15.
- **EF-2: Sale Reversed** — After step 6, a paid sale or order is reversed (refunded or cancelled). The system deducts the points earned on it from the customer's balance and records the deduction in the points history. Partial reversals and the case where the customer already spent the points are undecided; see OQ-9 and OQ-11.
- **EF-3: Payment Not Completed** — At step 3, the payment fails or remains pending. No points are awarded until the order is paid. (Follows BR-2.)

## Postconditions
- The points earned are added to the customer's loyalty balance.
- The points history shows the transaction with the points earned.
- The cumulative balance is updated and visible in the customer's account and to the cashier at the POS.

## Business Rules
- **BR-1:** Points are earned based on the amount spent in EUR at a configurable earn rate (e.g., 1 point per EUR 1). The actual rate, rounding, and qualifying amount are undecided; see OQ-1 and OQ-2.
- **BR-2:** Points are awarded on completed and paid sales only; unpaid, pending, or failed orders earn nothing. The exact moment of awarding is undecided (see OQ-6).
- **BR-3:** A reversed sale deducts the corresponding points.
- **BR-4:** Points are awarded only to customers who are loyalty members (have opted in) at the time of the purchase.
- **BR-5:** Points are awarded at most once per paid sale or order.
- **BR-6:** Points earned feed the rewards system (UC-034). Badges (UC-033) are not part of the MVP.
- **BR-7:** Enrolment in the loyalty programme is opt-in. No customer is enrolled automatically, and a customer who has not opted in earns no points and cannot redeem rewards (UC-034).
- **BR-8:** The purchase-related email sent to a customer who is not a loyalty member must include an invitation with a link to opt in. Today the only such email is the webshop order confirmation (see Implementation Notes). The email sent to a customer who is already a member does not need the invitation.

## Open Questions
Decisions needed from the product owner. Nothing below has been decided; the flows above avoid assuming an answer.

| ID | Question | Options |
|----|----------|---------|
| OQ-1 | What is the earn rate, and how is the result rounded? | (a) 1 point per EUR 1, rounded down; (b) 1 point per EUR 1, rounded to nearest; (c) other rate, e.g. 10 points per EUR 1; (d) fractional points kept |
| OQ-2 | Which amount counts toward points? | Incl. or excl. VAT; shipping cost included or excluded; amount before or after discount (UC-021 discounts); amount after a redeemed reward (UC-034) |
| OQ-4 | Can walk-in (anonymous) POS customers join? | (a) No, a customer record is required; (b) yes, via quick-register at the POS (UC-021 / UC-002 AF-6); (c) yes, with only an email or phone number |
| OQ-5 | Can points be claimed for a purchase made before identification? | (a) No; (b) cashier can add the customer shortly after the sale; (c) customer claims via receipt code; (d) guest webshop orders are matched to a member by email |
| OQ-6 | When are points awarded? | (a) When payment is confirmed; (b) when the order is placed; (c) after a delay, e.g. after the return period or after fulfilment |
| OQ-7 | Do points expire? | (a) Never; (b) fixed period after earning; (c) after a period of inactivity; and if yes, is the customer warned (email is the only channel as push is out of scope) |
| OQ-8 | Does customer-specific pricing (UC-007) affect earning? | (a) Points on the price actually paid; (b) customers with customer-specific prices earn no points; (c) different rate for them |
| OQ-9 | How are partial refunds handled? | (a) Proportional deduction; (b) full deduction only on full refund; (c) no deduction below a threshold |
| OQ-10 | Are bonus-point promotions (e.g., double-points weekends) in the MVP? | (a) No, future; (b) yes, admin-configurable multiplier for a date range; (c) per product or product type |
| OQ-11 | What if a reversal would make the balance negative because points were already spent? | (a) Allow a negative balance; (b) cap at zero; (c) block the reversal; (d) cancel the redeemed reward |
| OQ-12 | Where does the admin configure the earn rate and rewards catalogue? | (a) New UC "Configure Loyalty Programme" (recommended, see report); (b) add a loyalty section to UC-015 reference data. The admin interface needs this regardless of the choice |
| OQ-13 | Do B2B customers earn points? | (a) No, B2C only; (b) yes, same as B2C; (c) per-customer flag |
| OQ-14 | Should a points balance be adjustable manually by the admin (corrections, goodwill)? | (a) No, not in MVP; (b) yes, with a reason recorded |
| OQ-15 | Does opting in grant points retroactively for the purchase that triggered the opt-in (AF-4)? | (a) No, only purchases after opting in; (b) yes for the triggering order if opted in within a time window; (c) yes for all past orders |
| OQ-16 | Can a customer opt out, and what happens to the balance? | (a) No opt-out in the MVP; (b) opt-out with the balance forfeited; (c) opt-out with the balance kept and frozen; (d) opt-out with the balance and history erased (privacy request) |
| OQ-17 | Can the cashier opt a customer in at the POS? | (a) No, only the customer in the account or via the email link; (b) yes, with the customer's consent confirmed verbally; (c) yes, with confirmation by email. Relates to OQ-4 |
| OQ-18 | What consent wording and record is needed for opting in? | Plain confirmation; terms and privacy text shown with a recorded date; double opt-in by email |
| OQ-19 | Should customers who have not opted in be invited in other communications, such as the invoice email or the POS receipt? | (a) Only the webshop order confirmation (BR-8); (b) also the invoice email; (c) also the POS receipt (a printed link or code) |

## Implementation Notes
- Purchase-related emails that exist today (verified in the code): (1) the webshop order confirmation to the customer, sent after successful payment when the order has an email address (`server/checkoutService.ts`, `sendCustomerConfirmation`, via Resend); (2) an internal new-order notification to the admin (`sendAdminNotification`), which is not customer-facing and needs no invitation; (3) the invoice email with PDF attached, which an admin triggers manually for a sale that has an invoice number (route in `server/routes.ts`; see UC-008). No payment-failure email exists, and the POS sends no emailed receipt. The webshop order confirmation is therefore the only customer-facing email that is sent automatically after a purchase.
- The order confirmation needs to know whether the customer is a loyalty member, matching the order to a customer record, which is not guaranteed for guest checkout.
- A loyalty table already exists in the data model (customer reference, points, and deductions), but no routes, services, or components use it. It needs review against the decisions above (points history per transaction, expiry, opt-in status and date) before being relied upon.
- Points calculation must run when a POS sale is recorded (UC-021 step 14) and when the webshop payment confirmation is processed (UC-025 step 12), and must be idempotent because the payment provider can notify more than once.
- The account overview (UC-026) and the POS customer lookup (UC-021) need to expose the balance; no additional interface is introduced.

## Implemented By
- Status: **Not yet implemented** — planned MVP feature. A `loyalty` table exists in the schema but is not used by any route, service, or component.
