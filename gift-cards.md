# Wellface Gift Cards

Wellface gift cards make the perfect present for someone who deserves a treat. They can be used for any treatment or product at any Wellface clinic. All amounts are in GBP.

## Overview

- Available in preset amounts or custom values
- Redeemable at any Wellface clinic (Cobham, Esher, Wimbledon)
- Valid for **24 months** from the date of purchase
- Can be partially redeemed — the remaining balance carries forward
- Each gift card has a unique code in the format **WF-XXXX-XXXX** (uppercase letters and digits)

## How to purchase

### Online (giftcards.wellface.com)

Customers can purchase gift cards online at [giftcards.wellface.com](https://giftcards.wellface.com). The public site offers these preset amounts:

- £100, £250, £500, £1,000, £2,000, £3,000
- Custom amount (minimum £50, maximum £3,000)

Payment is processed via **Stripe Checkout** (card only). After completing the form and clicking "Proceed to Payment", the customer is redirected to Stripe's hosted checkout page. On successful payment, Stripe sends a `checkout.session.completed` webhook which triggers gift card creation, emails, and task generation automatically.

### In-clinic (via Hub dashboard)

Clinic staff can create gift cards manually through the Wellface Hub dashboard. The Hub offers these preset amounts:

- £50, £75, £100, £150, £200, £250, £300, £500
- Custom amounts are also supported (minimum £1)

Staff select a payment method, delivery method, and fill in purchaser/recipient details. The Hub also supports searching existing patients to auto-fill purchaser information.

### Contacting the clinic

Customers can also purchase by phone, WhatsApp, or email:

| Clinic | Phone | WhatsApp |
|---|---|---|
| Cobham | 01932 690 800 | [+44 7471 741680](https://wa.me/447471741680) |
| Esher | 01372 826 649 | [+44 7471 741680](https://wa.me/447471741680) |
| Wimbledon | 020 8914 8400 | [+44 7511 841998](https://wa.me/447511841998) |

## Payment methods

- **Stripe** — used for all online purchases via Stripe Checkout; also available for in-clinic purchases
- **Dojo** — credit/debit card terminal payment, used for in-clinic purchases only
- **Cash** — used for in-clinic purchases only

## Delivery methods

- **Email Now** — the gift card is emailed to the recipient immediately after purchase
- **Email Scheduled** — the gift card is emailed to the recipient on a chosen future date (e.g. a birthday)
- **Post** — a physical gift card is printed and posted to a postal address provided by the purchaser
- **Collection** — the purchaser collects the gift card in-clinic; this option is only available in the Hub dashboard (not on the public purchase site)

For email delivery, the recipient's email address is required. For postal delivery, a full postal address (line 1, optional line 2, city, postcode) is required. For collection, neither email nor address is needed.

## Gift card statuses

| Status | Meaning |
|---|---|
| `pending_delivery` | Purchased but not yet delivered to the recipient |
| `delivered` | Delivered to the recipient (email sent or post/collection confirmed) |
| `partially_redeemed` | Some of the balance has been used but a remaining balance exists |
| `fully_redeemed` | The entire balance has been used |
| `expired` | The gift card has passed its 24-month expiry date |
| `cancelled` | The purchase was refunded and the gift card is void |

## Redemption

To redeem a gift card, the recipient presents their code (WF-XXXX-XXXX) when attending an appointment at any Wellface clinic. Staff look up the code in the Hub dashboard, verify the balance, and record a redemption for the treatment amount. The balance is reduced accordingly.

- If the redemption amount equals the remaining balance, the status changes to `fully_redeemed`
- If the redemption amount is less than the remaining balance, the status changes to `partially_redeemed` and the remaining balance is available for future use
- Redemption cannot exceed the current balance
- Expired or fully redeemed gift cards cannot be redeemed

Each redemption is recorded with the amount, the staff member who processed it, optional notes, and a timestamp.

## Expiry

Gift cards expire **24 months (730 days)** from the date of purchase. Expired gift cards cannot be redeemed.

## Email notifications

Three types of email are sent when a gift card is purchased:

1. **Buyer confirmation** — sent to the purchaser's email, confirming the purchase with the gift card code, amount, recipient name, expiry date, and delivery information. Subject: "Your Wellface Gift Card Purchase — WF-XXXX-XXXX"

2. **Recipient gift card email** — sent to the recipient's email with the gift card code, amount, expiry date, any personal message from the purchaser, and a "Book an Appointment" button linking to wellface.com. This is sent immediately for "Email Now" delivery, on the scheduled date for "Email Scheduled" delivery, and not sent at all for "Post" or "Collection" delivery. Subject: "You've received a Wellface gift card!"

3. **Admin notification** — sent to contact@wellface.com with full details of the purchase including code, amount, purchaser, recipient, delivery method, delivery date, postal address (if applicable), expiry, and personal message. Subject: "Gift Card Purchased — WF-XXXX-XXXX — £amount"

All emails are sent via Postmark from contact@wellface.com (buyer and recipient) or web@wellface.com (admin notification).

### Scheduled email processing

A cron job runs every 5 minutes to check for gift cards with scheduled email delivery whose delivery date has arrived. It sends the recipient email and marks the card as delivered.

### Resending emails

Staff can manually resend the recipient email from the Hub dashboard for email-delivery gift cards. This also marks the card as delivered and completes any associated task.

## Task generation

When a gift card is purchased, a task is automatically created in the Hub task system:

- **Email Now**: task is created and immediately marked as done if the recipient email was sent successfully; otherwise it remains open
- **Email Scheduled**: task is created with the scheduled delivery date as the due date
- **Post**: task is created with instructions to print and post the gift card, including the recipient's address and a due date

Tasks have the title format: "Gift Card WF-XXXX-XXXX — [action] to [recipient name]"

Tasks are automatically completed when the recipient email is successfully sent (for email delivery) or when staff manually marks the gift card as delivered (for post/collection).

## Refund handling

When a Stripe charge is refunded, the `charge.refunded` webhook automatically:

1. Sets the gift card status to `cancelled` and the balance to £0
2. Marks any open tasks for that gift card as done (completed by "System (Refund)")

Refunds are only applicable to gift cards purchased online via Stripe. For in-clinic purchases made via Dojo or cash, refunds are handled outside the system.

## Hub dashboard

The gift cards page in the Hub dashboard (hub.wellface.com) provides:

- **Summary stats**: total sold, total revenue, total redeemed, total outstanding balance, pending delivery count, pending post count
- **Gift card list**: filterable by status, searchable by code/name/email, showing code, amount, balance, recipient, delivery method, status, and purchase date
- **Detail view**: clicking a gift card shows full details, redemption history, and email logs
- **Actions**: create new gift cards, redeem balances, mark as delivered, send/resend emails, edit details (recipient, delivery method, personal message, etc.)
- **Real-time updates**: the dashboard refreshes automatically via server-sent events when gift cards are purchased or updated

## Personal messages

Purchasers can include an optional personal message (up to 200 characters) which is displayed in the recipient's gift card email as a styled quote with the purchaser's name.
