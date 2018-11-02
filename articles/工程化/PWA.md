# PWA

Progressive Web Apps:

* Reliable
* Fast
* Engaging

## Baseline PWA Checklist

* HTTPS
* responsive on tablets & mobile devices
* All app URLs load while offline (Service Worker)
* Metadata provided for Add to Home screen
* First load fast even on 3G
* Site works cross-browser
* Page transitions don't feel like they block on the network
* Each page has a URL

## Exemplary PWA Checklist

Indexability & social

- [ ] Site's content is indexed by Google
- [ ] Schema.org metadata is provided where appropriate
- [ ] Social metadata is provided where appropriate
- [ ] Canonical URLs are provided when necessary
- [ ] Pages use the History API

User Experience

* Content doesn't jump as the page loads
* Pressing back from a detail page retains scroll position on the previous list page
* When tapped, inputs aren't obscured by the on screen keyboard
* Content is easily shareable from standalone or full screen mode
* Site is responsive across phone, tablet and desktop screen sizes
* Any app install prompts are not used excessively（不过分使用安装提示）
* The Add to Home Screen prompt is intercepted

Performance

* First load very fast even on 3G

Caching

* Site uses cache-first networking
* Site appropriately informs the user when they're offline

Push notifications

- [ ] Provide context to the user about how notifications will be used
- [ ] UI encouraging users to turn on Push Notifications must not be overly aggressive.
- [ ] Site dims the screen when permission request is showing
- [ ] Push notifications must be timely, precise and relevant
- [ ] Provides controls to enable and disable notifications

Additional features

- [ ] User is logged in across devices via Credential Management API
- [ ] User can pay easily via native UI from Payment Request API.

## 参考

* [Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)
