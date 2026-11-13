# UC-034: Redeem Loyalty Rewards

A registered customer exchanges accumulated loyalty points for a reward from the rewards catalogue and benefits from it on a purchase at the POS or in the webshop. This gives the loyalty programme tangible value and encourages customers to keep their balance active.

## Metadata
- **Domain:** Customer / Loyalty / Sales
- **Primary Actor:** Registered Customer
- **Secondary Actors:** POS User / Cashier (POS purchases)
- **Priority:** Could
- **Status:** Draft (MVP)
- **Interface:** Storefront, POS Terminal

## Scope (MVP)
- **In scope:** Browsing rewards and redeeming them in the existing storefront customer account (UC-026); using a reward during webshop checkout (UC-025); the cashier applying a reward at the POS after looking up the customer (UC-021).
- **Out of scope:** A separate Loyalty App, QR-code redemption from an app, push notifications, badges (UC-033), challenges (UC-035), and loyalty login/verification (UC-031). These are not part of the MVP.
- **Related capability (not part of this UC):** The admin configures the rewards catalogue and earn rate. See Preconditions and Open Question OQ-12 in UC-032.

## Preconditions
- The customer is a registered customer who is a loyalty member (UC-032, see OQ-3 there) and is logged in (webshop) or has been selected by the cashier (POS).
- The customer has sufficient points for at least one reward.
- The rewards catalogue has been configured by the admin, with at least one active reward (name, description, points cost, availability).

## Main Flow
1. The registered customer opens the rewards overview in their account (UC-026).
2. The system shows the customer's points balance and the available rewards, listing for each reward: name, description, points required, and availability.
3. The customer selects a reward to redeem.
4. The system confirms the customer has sufficient points for the selected reward and asks the customer to confirm the redemption.
5. The customer confirms.
6. The system deducts the required points from the customer's balance.
7. The system issues the reward to the customer and records the redemption in the points history. The form of the reward (voucher code or discount applied directly at checkout) is undecided; see OQ-1.
8. The customer proceeds to webshop checkout (UC-025) and the system offers the issued reward for this order.
9. The customer chooses to use the reward.
10. The system verifies the reward is valid and applies it to the order, adjusting the order total.
11. The customer completes payment (UC-025), and the system marks the reward as used.

## Alternative Flows
- **AF-1: Redeem at the POS** — At step 8, instead of webshop checkout, the customer is in the store. The cashier looks up the customer and selects them on the sale (UC-021 step 7). The cashier sees the customer's balance and issued rewards, selects the reward to apply, and the system verifies and applies it to the sale total (see OQ-4 for how this combines with POS discounts). The flow continues at step 11 with the sale payment (UC-021 steps 12-14).
- **AF-2: Redeem a Reward at the POS Without Prior Redemption Online** — At step 1, the customer asks the cashier to redeem points. The cashier selects the customer, and the system shows the available rewards and the balance. The cashier selects a reward, the system confirms sufficient points (step 4), and the flow continues at step 6, then at AF-1. *Whether a cashier can redeem on the customer's behalf is undecided; see OQ-9.*
- **AF-3: Enter a Reward Code at Checkout** — At step 9, if the reward is a voucher (OQ-1 option a), the customer enters the reward code instead of selecting it from the offered rewards. The flow continues at step 10.

## Exception Flows
- **EF-1: Insufficient Points** — At step 4, the customer does not have enough points. The system shows the shortfall and the points still needed. The redemption does not proceed.
- **EF-2: Expired Reward** — At step 10, the issued reward has passed its validity date (see OQ-3). The system rejects the reward and the order total is unchanged.
- **EF-3: Already Used** — At step 10, the reward has already been used. The system rejects the attempt.
- **EF-4: Reward No Longer Available** — At step 4, the reward was deactivated or is out of stock (for limited-availability rewards) after the customer viewed the list. The system informs the customer and no points are deducted.
- **EF-5: Reward Not Applicable to the Order** — At step 10, the reward cannot be applied to the order (e.g., it requires a product not in the cart, or a minimum order amount is not met). The system explains why and the order total is unchanged.
- **EF-6: Payment Not Completed** — At step 11, the payment fails or is cancelled. The reward is not marked as used and remains available (see OQ-8 for whether the points are returned).

## Postconditions
- Points are deducted from the loyalty balance.
- The reward is applied to the transaction (discount, free product, etc., see OQ-1).
- The redemption is recorded in the points history, visible in the customer's account and to the cashier at the POS.
- The reward cannot be used again.

## Business Rules
- **BR-1:** Rewards have a fixed point cost.
- **BR-2:** A reward can only be redeemed when the customer's balance covers its point cost.
- **BR-3:** Each issued reward can only be used once.
- **BR-4:** An issued reward has a validity period and cannot be used after it has passed (period undecided, see OQ-3).
- **BR-5:** Redemption integrates with POS (UC-021) and webshop checkout (UC-025).
- **BR-6:** Reward types available in the MVP are undecided (see OQ-2). The previously drafted types were: percentage discount, fixed discount, free product, free upgrade.

## Open Questions
Decisions needed from the product owner. Nothing below has been decided; the flows above avoid assuming an answer. Earning-related questions are in UC-032 (OQ-1 to OQ-14).

| ID | Question | Options |
|----|----------|---------|
| OQ-1 | How is a reward delivered? | (a) Voucher with a unique code that the customer or cashier enters later; (b) direct discount applied at checkout/POS at the moment of redemption (no voucher); (c) both |
| OQ-2 | Which reward types are in the MVP? | Percentage discount; fixed-amount discount; free product; free upgrade (e.g., larger size). Suggest starting with one or two types |
| OQ-3 | How long is a voucher valid? | (a) Fixed period (e.g., 30/60/90 days); (b) configurable per reward; (c) no expiry. Do unused expired vouchers return their points? |
| OQ-4 | Can a reward be combined with other price reductions? | With POS discounts (UC-021 AF-4/AF-5); with customer-specific prices (UC-007); with free shipping (UC-030); with a webshop discount |
| OQ-5 | Can several rewards be stacked on one order? | (a) One reward per order; (b) several, different rewards; (c) unlimited |
| OQ-6 | Do reward discounts earn points? | Points on the amount after the reward (see UC-032 OQ-2), on the original amount, or none |
| OQ-7 | Does a reward discount apply to shipping cost? | Product amount only, or also shipping |
| OQ-8 | When a payment fails or the sale is reversed after redemption, what happens to the points? | (a) Reward is restored and points stay deducted; (b) points are returned; (c) reward is lost |
| OQ-9 | Can the cashier redeem on behalf of a customer, and how does the cashier verify identity? | (a) Cashier selects customer, no further check; (b) customer shows a code or confirms by email; (c) redemption only in the customer's account, cashier only applies issued vouchers |
| OQ-10 | Is a reward restricted by product, product type, minimum order value, or channel (POS only, webshop only)? | None; per-reward restrictions configured in the catalogue |
| OQ-11 | How is stock handled for a free-product reward? | Normal sale line at zero price (stock deducted), or separate handling; and how is it shown on receipts, invoices (UC-008), and VAT breakdown |
| OQ-12 | Can a customer see the cost of rewards they cannot yet afford? | Show all rewards with shortfall, or only affordable rewards |
| OQ-13 | Can a customer cancel an issued reward to get points back? | No; yes within a period; yes any time before use |

## Implementation Notes
- Applying a reward changes the order total, so it must be reflected in the VAT breakdown (per VAT class), in the payment amount sent to the payment provider (UC-025), and in the POS receipt (UC-021).
- Deducting points and marking a reward as used must be atomic and protected against double use (e.g., two simultaneous checkouts).
- The existing `loyalty` table in the schema has no usage yet and does not model a rewards catalogue or issued rewards; the data model needs to be defined during specification.

## Implemented By
- Status: **Not yet implemented** — planned MVP feature.
