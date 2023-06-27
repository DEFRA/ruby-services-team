# Renewal Reminders

Currently, for 'digital' registrations, the following renewal reminders are sent

## First Renewal Reminder

This reminder is sent **via email** when a valid contact email is provided. It is dispatched **42 days** before the expiry date. The option to enable or disable this reminder can be controlled using a feature toggle - `send_first_email_reminder`

## Second Renewal Reminder

Similarly, this reminder is also sent **via email** when a valid contact email is provided. It is dispatched **28 days** before the expiry date. The option to enable or disable this reminder can be controlled using a feature toggle - `send_second_email_reminder`

## Final Renewal Reminder

In addition, a final reminder is transmitted **via text message** when a valid contact mobile phone number is provided. This communication is issued **21 days** prior to the expiration date. The option to enable or disable attempting to send out this reminder via text is controlled by a feature toggle - `send_digital_renewal_sms`

In cases where no valid mobile phone number is provided or the feature toggle allowing texts is not toggled on, this will be sent **via letter**.
