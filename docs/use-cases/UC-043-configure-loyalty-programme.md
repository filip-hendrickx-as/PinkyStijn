# UC-043: Configure Loyalty Programme

The admin sets how fast customers earn loyalty points and maintains the catalogue of rewards they can redeem. This gives the business control over the cost and attractiveness of the loyalty programme without involving a developer.

## Metadata
- **Domain:** Customer / Loyalty / Operations
- **Primary Actor:** Admin
- **Secondary Actors:** None
- **Priority:** Could
- **Status:** Draft (MVP)
- **Interface:** Admin Dashboard

## Scope (MVP)
- **In scope:** Setting the earn rate; adding, changing, activating, and deactivating rewards in the rewards catalogue.
- **Out of scope:** Reward types other than a fixed EUR discount (percentage discount, free product, free upgrade), voucher codes, bonus-point promotions (see UC-032 OQ-6), and manual balance adjustments (see UC-032 OQ-8).
- **Consumed by:** UC-032 (Earn Loyalty Points) uses the earn rate; UC-034 (Redeem Loyalty Rewards) uses the rewards catalogue.

## Preconditions
- The admin is authenticated and has admin privileges.

## Main Flow
1. The admin requests the loyalty programme settings.
2. The system shows the current earn rate (points earned per EUR 1 spent; the default is 1 point per EUR 1) and the rewards catalogue, listing for each reward: name, description, points cost, reward value in EUR, and whether it is active.
3. The admin changes the earn rate and confirms.
4. The system verifies the earn rate is valid, records it, and shows the refreshed settings.
5. The admin indicates they want to add a new reward.
6. The system asks for the reward details: name, description, points cost, reward value in EUR (a fixed discount), and whether the reward is active.
7. The admin provides the details and confirms.
8. The system verifies the details are complete and valid, records the reward, and shows the refreshed catalogue.

## Alternative Flows
- **AF-1: Change an Existing Reward** — At step 5, instead of adding a reward, the admin selects an existing reward and indicates they want to change it. The system shows the current values. The admin adjusts the desired fields and confirms. The system verifies and records the changes. The flow continues at step 8.
- **AF-2: Deactivate or Reactivate a Reward** — At step 5, instead of adding a reward, the admin selects an existing reward and changes only whether it is active. The system records the change and the flow continues at step 8. An inactive reward is no longer offered to customers at the POS or in the webshop.
- **AF-3: Add a Reward Without Changing the Earn Rate** — At step 3, the admin leaves the earn rate unchanged and goes straight to adding a reward. The flow continues at step 5.

## Exception Flows
- **EF-1: Invalid Earn Rate** — At step 4, the earn rate is missing, zero, or negative. The system explains the problem and does not record the change. The previous earn rate stays in effect.
- **EF-2: Invalid Reward Details** — At step 8 (and in AF-1), required details are missing, or the points cost or the reward value is zero or negative. The system explains the problem and does not record the reward.

## Postconditions
- The earn rate is recorded and applies to the points calculation in UC-032 (for which purchases, see OQ-2).
- The rewards catalogue is recorded, and active rewards are available for redemption in UC-034 at the POS and in the webshop.

## Business Rules
- **BR-1:** The earn rate is expressed as points per EUR 1 spent. It must be greater than zero. The default is 1 point per EUR 1.
- **BR-2:** Every reward has a name, a points cost greater than zero, and a reward value in EUR greater than zero.
- **BR-3:** The only reward type in the MVP is a fixed EUR discount.
- **BR-4:** Only active rewards are offered to customers.
- **BR-5:** Only an admin can change the loyalty programme settings.

## Open Questions
Decisions needed from the product owner. Nothing below has been decided.

| ID | Question | Options |
|----|----------|---------|
| OQ-1 | May the earn rate be a fraction (e.g., 0.5 points per EUR 1) or only a whole number? Rounding down of the result is already decided in UC-032. | (a) Whole numbers only; (b) decimals allowed |
| OQ-2 | Does a changed earn rate apply only to later purchases, or can it also recalculate past ones? | (a) Later purchases only; (b) recalculation of past purchases; (c) effective from a chosen date |
| OQ-3 | Can a reward be removed, or only deactivated? | (a) Deactivate only; (b) removal allowed when it has never been redeemed; (c) removal always allowed (with confirmation) |
| OQ-4 | Is a record needed of who changed the earn rate or a reward and when? | (a) No; (b) yes, a change history |

## Implementation Notes
- The earn rate is a single system-wide value; the existing system-parameter mechanism used for the free shipping threshold (UC-015 AF-4) is a possible home for it, to be confirmed in the specification.
- The rewards catalogue needs its own data structure; the existing `loyalty` table does not model it.
- Earn rate and catalogue changes take effect immediately across POS and webshop, as with other reference data (UC-015).

## Implemented By
- Status: **Not yet implemented** — planned MVP feature.
