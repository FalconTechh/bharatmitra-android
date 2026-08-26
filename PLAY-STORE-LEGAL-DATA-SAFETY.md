# BharatMitra AI — Play Store Legal/Data Safety Update

Version: 1.1.0

## New user-facing features
- BharatMitra Search
- Mandi Bhav
- Local Help

## Removed features
- UPI QR
- Document Scanner

## Privacy/Data Safety implementation notes

The app should only declare data practices that match the shipped build and Firebase configuration.

### Data used by features
- Account: name, email, optional profile photo.
- Family Safety: location only when the user explicitly enables live location sharing; background location is used only for that feature.
- Business Manager: user-entered business records.
- Search: search queries may be sent to third-party/open search services. Recent search history is stored locally on the device.
- Local Help: location is requested when the user uses nearby search. User-created local listings are stored on the device.
- Mandi Bhav: optional data.gov.in API key is stored locally; mandi queries are sent to the government data API when the user searches.
- Notifications: OneSignal device token/identifier may be used for push delivery.

### Data sharing
Search queries and other feature requests may be transmitted to the relevant external provider needed to return the requested result. The app does not claim that third-party websites are controlled by BharatMitra.

### Important Play Console consistency
Before publishing, complete the Play Console Data Safety form from the final release build, including every SDK/provider actually enabled in production. Do not claim "no data collected" if Firebase, OneSignal, location, search providers, or other services transmit data.

## User-facing legal pages
- `www/privacy-policy.html`
- `www/terms.html`

These pages should be hosted at a stable public HTTPS URL before Play Store submission. If the app currently opens them locally inside the app, also publish the same content on a public HTTPS web page for the Play Console privacy-policy URL.
