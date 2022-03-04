# Technical newsletter for developers 2022-03

💥 Draft! 💥

This newsletter was sent in March 2022.

- [Technical newsletter for developers 2022-03](#technical-newsletter-for-developers-2022-03)
- [Recurring charge changes](#recurring-charge-changes)
- [Some new thing 2](#some-new-thing-2)
- [Reminders](#reminders)
  - [Please check your eCom API calls](#please-check-your-ecom-api-calls)
  - [Use the API Dashboard to find problems with your integration](#use-the-api-dashboard-to-find-problems-with-your-integration)

# Recurring charge changes

From **August 1st, 2022** new rules for charge creation will be enforced:

- The `amount` of a charge is flexible but can not be higher than the `agreement price`.
- For an agreement with a `campaign`, the `amount` of a charge is flexible but can not be higher than the campaign price. After the campaign expires the `amount` of a charge can not be higher than the `agreement price`.
If the agreement was created with an initial charge and the initial charge amount is the same amount as the campaign price, then no new charges can be created until the next interval for the campaign.

See the [recurring documentation]([https://github.com/vippsas/vipps-recurring-api/blob/master/vipps-recurring-api.md#create-charge) on how the new enforced rules are (will be enforced for normal and variable amount charges).

# Some new thing 2

Text here.

# Reminders

## Please check your eCom API calls

We are working on eliminating incorrect API use. Although we always respond to
incorrect API calls with a sensible HTTP status (usually `HTTP 400 Bad Request`)
and an informative error message in the response body, we see that some merchant
and partners keep making incorrect API calls.

Please:
- Monitor the responses you get when making API calls
- Log all errors
- Fix errors as quickly as possible
- Use the API Dashboard
- Contact us if there is anything we can help with

One example: Far too many calls to
[`POST:/ecomm/v2/payments`](https://vippsas.github.io/vipps-ecom-api/#/Vipps_eCom_API/initiatePaymentV3UsingPOST)
use an incorrectly formatted phone number.
The effect is that the user's phone number is not correctly pre-filled on
the Vipps landing page.
Please make sure you send the `mobileNumber` in `91234567` format, not
`+47 91 23 45 67` or something else.
We have previously tried to respond with `HTTP 400 Bad Request` (as we should)
for incorrectly formatted phone numbers, but that broke _a lot_  of integrations,
so we decided to accept the incorrect API calls even though they give a poor
user experience.

See:
* [Use the API Dashboard to find problems with your integration](#use-the-api-dashboard-to-find-problems-with-your-integration)
* "Common errors" in the
  [eCom API FAQ](https://github.com/vippsas/vipps-ecom-api/blob/master/vipps-ecom-api-faq.md)

## Use the API Dashboard to find problems with your integration

The API Dashboard is available to all merchants for both the production and test environments,
and is an easy way to see if you are using the Vipps APIs correctly.
Think of it as a "health check", that you can use to see if there are any
problems you need to investigate.

See it on
[portal.vipps.no](https://portal.vipps.no)
under the "Utvikler" ("Developer") tab.
Here's an example for the Vipps eCom API's `/refund` endpoint:

![API Dashboard example](images/2021-02-api-dashboard-example.png)

See:
[API Dashboard](https://github.com/vippsas/vipps-developers#api-dashboard).
