# UC-034: Redeem Loyalty Rewards

A registered customer who has joined the loyalty programme exchanges accumulated points for a fixed EUR discount on a purchase, at webshop checkout or at the POS. This gives the loyalty programme tangible value and encourages customers to keep their balance active.

## Metadata
- **Domain:** Customer / Loyalty / Sales
- **Primary Actor:** Registered Customer
- **Secondary Actors:** POS User / Cashier (POS purchases)
- **Priority:** Could
- **Status:** Draft (MVP)
- **Interface:** Storefront, POS Terminal

## Scope (MVP)
- **In scope:** Viewing the points balance and the available rewards in the existing storefront customer account (UC-026); applying a reward as a direct discount during webshop checkout (UC-025); the cashier applying a reward as a direct discount at the POS after looking up the customer (UC-021).
- **Out of scope:** A separate Loyalty App, push notifications, badges (UC-033), challenges (UC-035), loyalty login/verification (UC-031), voucher codes (a reward is applied directly, nothing is issued for later use), reward types other than a fixed EUR discount (percentage discount, free product, free upgrade), and refunds or reversals of sales.
- **Refunds and reversals:** The application has no refund or reversal capability yet, so returning points or restoring a reward after a reversed sale is not part of the MVP. It needs to be specified when a refund capability is introduced.
- **Configuration:** The rewards catalogue (name, description, points cost, reward value in EUR, active flag) is maintained by the admin in UC-043 (Configure Loyalty Programme).
- **Decided:** A reward is a direct discount at checkout or POS (BR-3). The only reward type is a fixed EUR discount (BR-4). Opting in is required (UC-032 BR-7).

## Preconditions
- The customer is a registered customer who has opted in to the loyalty programme (UC-032 BR-7, AF-3), and is logged in (webshop) or has been selected by the cashier (POS).
- The customer has sufficient points for at least one reward.
- The rewards catalogue has been configured by the admin (UC-043) with at least one active reward.

## Main Flow
1. The registered customer, logged in, proceeds to checkout (UC-025) with items in the cart.
2. The system shows the customer's points balance and the active rewards, listing for each reward: name, description, points required, and discount value in EUR. Whether rewards the customer cannot yet afford are shown is undecided; see OQ-8.
3. The customer selects a reward to apply to the order.
4. The system verifies that the reward is active, that the customer's balance covers its points cost, and that the reward value does not exceed the purchase amount.
5. The system applies the fixed EUR discount to the order and shows the adjusted order total with the VAT breakdown.
6. The customer confirms the order and pays through the payment provider (UC-025).
7. The system deducts the points cost from the customer's balance and records the redemption, with the order number, in the points history. The moment of deduction (when the reward is selected, when the order is placed, or when payment is confirmed) is undecided; see OQ-5.
8. The system shows the order confirmation with the discount that was applied.

## Alternative Flows
- **AF-1: Browse Rewards in the Account** — Before step 1, the registered customer opens their account (UC-026) and views their points balance and the available rewards, with the same details as in step 2. Nothing is redeemed. The use case ends or the customer continues at step 1.
- **AF-2: Redeem at the POS** — At step 1, instead of the webshop, the customer is in the store. The cashier looks up the customer and selects them on the sale (UC-021 step 7). The cashier sees the customer's balance and the active rewards (step 2) and, at the customer's request, selects a reward for the sale (step 3). The system verifies and applies the discount to the sale total (steps 4-5). Payment follows UC-021 steps 12-14 instead of step 6. The flow continues at step 7. How a reward combines with POS discounts (UC-021 AF-4/AF-5) is undecided; see OQ-1.

## Exception Flows
- **EF-1: Insufficient Points** — At step 4, the customer does not have enough points. The system shows the shortfall and the points still needed. The reward is not applied.
- **EF-2: Reward No Longer Available** — At step 4, the reward was deactivated by the admin after the customer viewed the list. The system informs the customer and the reward is not applied.
- **EF-3: Reward Exceeds the Purchase Amount** — At step 4, the value of the reward is higher than the purchase amount it would be applied to. The system does not let the order total go below zero (BR-6). What happens to the excess value, and whether the reward is blocked, is undecided; see OQ-9. Which amounts make up the purchase amount is undecided; see OQ-4.
- **EF-4: Payment Not Completed** — At step 6, the payment fails or is cancelled. The order is not paid. Whether points were already deducted at that moment and are returned depends on the timing decided in OQ-5.

## Postconditions
- Points are deducted from the loyalty balance.
- The fixed EUR discount is applied to the transaction.
- The redemption is recorded in the points history, visible in the customer's account and to the cashier at the POS.

## Business Rules
- **BR-1:** Rewards have a fixed point cost.
- **BR-2:** A reward can only be applied when the customer's balance covers its point cost and the reward is active.
- **BR-3:** A reward is applied as a direct discount at webshop checkout or at the POS. No voucher codes are issued.
- **BR-4:** The only reward type in the MVP is a fixed EUR discount.
- **BR-5:** Redemption integrates with POS (UC-021) and webshop checkout (UC-025).
- **BR-6:** A reward applied to a purchase can be worth at most the purchase amount, so the order or sale total never goes below zero. Whether the purchase amount includes shipping and VAT is undecided (see OQ-4 and UC-032 BR-2 for the earning side); the handling of any excess value is undecided (see OQ-9).
- **BR-7:** Only customers who have opted in to the loyalty programme can redeem rewards (UC-032 BR-7).

## Open Questions
Decisions needed from the product owner. Nothing below has been decided; the flows above avoid assuming an answer. Earning- and enrolment-related questions are in UC-032; configuration questions are in UC-043.

| ID | Question | Options |
|----|----------|---------|
| OQ-1 | Can a reward be combined with other price reductions? | With POS discounts (UC-021 AF-4/AF-5); with customer-specific prices (UC-007); with free shipping (UC-030); with a webshop discount |
| OQ-2 | Can several rewards be applied to one order? | (a) One reward per order; (b) several, different rewards; (c) the same reward repeatedly |
| OQ-3 | Do purchases paid partly with a reward earn points on the amount after the reward? (Linked to UC-032 OQ-1) | Points on the amount after the reward, on the original amount, or none |
| OQ-4 | Which amounts make up the "purchase amount" a reward is capped at (BR-6): does it include shipping and VAT? Does the discount apply to shipping cost? | Product amount only; product amount including VAT; also shipping |
| OQ-5 | When are points deducted, and what happens to them if payment fails? | (a) Deducted when the reward is selected and returned if payment fails; (b) deducted when the order is placed; (c) deducted only when payment is confirmed |
| OQ-6 | How does the cashier verify the customer's identity and consent before applying a reward at the POS? | (a) Cashier selects the customer, no further check; (b) customer confirms by a code or email; (c) only the customer's own account can trigger redemption |
| OQ-7 | Is a reward restricted by product, product type, minimum order value, or channel (POS only, webshop only)? | None; per-reward restrictions configured in the catalogue (UC-043) |
| OQ-8 | Can a customer see the cost of rewards they cannot yet afford? | Show all rewards with the shortfall, or only affordable rewards |
| OQ-9 | What happens to the excess value when a reward is worth more than the purchase amount (BR-6)? | (a) Excess is lost; (b) excess is converted back into points; (c) the reward cannot be applied to a purchase smaller than its value (blocked). A remainder kept for later is not possible as no voucher is issued, unless the product owner decides otherwise |

## Implementation Notes
- Applying a reward changes the order total, so it must be reflected in the VAT breakdown (per VAT class), in the payment amount sent to the payment provider (UC-025), and in the POS receipt (UC-021).
- Deducting points must be atomic and protected against double use (e.g., two simultaneous checkouts spending the same points).
- The existing `loyalty` table in the schema has no usage yet and does not model redemptions or points history; the data model needs to be defined during specification. The rewards catalogue is configured in UC-043.

## Implemented By
- Status: **Not yet implemented** — planned MVP feature.
