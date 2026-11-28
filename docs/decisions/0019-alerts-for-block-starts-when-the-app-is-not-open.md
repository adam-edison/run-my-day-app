# DR-0019: Alerts for Block Starts When the App Is Not Open

**Status:** Proposed  
**Date:** 2026-11-27

## Context

A block can be due while the app is not open. A suspended phone app, or a closed browser tab, runs no code.

* iOS: silent push is not guaranteed and is throttled. Background audio used only to stay awake is rejected in App Store review.
* Android: starting a foreground service from the background has been restricted since Android 12.
* Android: an app that targets Android 13 or higher does not get the exact alarm permission at install. The user must grant it, and the user or the system can revoke it.
* Chrome: a hidden tab's single timer fires at most 1 second late. A closed tab receives web push while Chrome is running. No API schedules a notification ahead of time.

Example: at 09:40 the Workout event moves from 10:00 to 09:45. The phone is locked, and the laptop's tab is closed.

## Decision

When the app is not open, a block start is shown as an alert. Tapping the alert opens the app, and the block runs.

Phones:

* iOS uses AlarmKit. Android uses an exact alarm.
* On Android, setup asks the user to grant the exact alarm permission. The app opens the system "Alarms & reminders" settings screen for this.
* On Android, the app checks the permission each time it opens. Without the permission, the app shows only the request to grant it, and the rest of the app cannot be used.
* Alerts are scheduled on the phone from the last calendar read, so they work offline.
* When the calendar changes, the server sends a visible push, and the alert is rescheduled.

Web:

* With the tab open, even hidden, the block starts by itself.
* With the tab closed, the server sends a web push at each block start.

Example: at 09:40 a push shows "Workout starts 09:45" on the phone. At 09:45 the phone's alarm rings, and the laptop shows a notification. The alarm is tapped, and the Workout block runs on the phone.

## Alternatives Considered

The block starts and plays audio by itself on phones, rejected:

* On iOS, silent push is not guaranteed, and background audio used only to stay awake is rejected in App Store review.
* Android and iOS would behave differently.

A desktop app outside the browser, rejected:

* It is outside the Expo stack.

## Rationale

* Phones and web closed tabs behave the same: an alert you tap.
* A phone alarm rings when the phone is locked and offline.

## Consequences

* With Chrome fully quit, or the tab closed while offline, no web alert appears.
* Alerts after a calendar change depend on the server reading the calendar. That is decided with calendar integration.
* The status stays Proposed until two trials pass:
  * Phones: tapping the alert opens the app into the block.
  * Expo web: audio keeps playing in a hidden tab.

## Sources

### [Configuring background execution modes - Apple Developer Documentation](https://developer.apple.com/documentation/xcode/configuring-background-execution-modes)

Retrieved 2026-11-27.

- Fact: A phone app in the background is suspended unless a background mode keeps it running.
  > "Typically, an app is in a suspended state when it’s in the background."

### [Pushing background updates to your App - Apple Developer Documentation](https://developer.apple.com/documentation/usernotifications/pushing-background-updates-to-your-app)

Retrieved 2026-11-27.

- Fact: On iOS, silent push is not guaranteed and is throttled.
  > "The system treats background notifications as low priority: you can use them to refresh your app’s content, but the system doesn’t guarantee their delivery. In addition, the system may throttle the delivery of background notifications if the total number becomes excessive."

### [App Review Guidelines - Apple Developer](https://developer.apple.com/app-store/review/guidelines/)

Retrieved 2026-11-27.

- Fact: Background audio used only to stay awake is rejected in App Store review.
  > "Multitasking apps may only use background services for their intended purposes: VoIP, audio playback, location, task completion, local notifications, etc."

### [Restrictions on starting a foreground service from the background - Android Developers](https://developer.android.com/develop/background-work/services/fgs/restrictions-bg-start)

Retrieved 2026-11-27.

- Fact: On Android, starting a foreground service from the background has been restricted since Android 12.
  > "Apps that target Android 12 (API level 31) or higher can't start foreground services while the app is running in the background, except for a few special cases."

### [Heavy throttling of chained JS timers beginning in Chrome 88 - Chrome for Developers](https://developer.chrome.com/blog/timer-throttling-in-chrome-88)

Retrieved 2026-11-27.

- Fact: In Chrome, a hidden tab's single timer fires at most 1 second late.
  > "The *chain count* is less than 5."
  > "The browser will check timers in this group once per **second**."

### [Push Notifications FAQ - web.dev](https://web.dev/articles/push-notifications-faq)

Retrieved 2026-11-27.

- Fact: A closed tab receives web push while Chrome is running.
  > "This means the browser can have no windows open, and you'll still receive the push message in your service worker, because the browser in running in the background."
- Fact: With Chrome fully quit, no web alert appears.
  > "The only time a push won't be received is when the browser is completely closed, i.e. not running at all (no marking)."

### [Push API - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Push_API)

Retrieved 2026-11-27.

- Fact: A web push reaches the site's service worker when no tab is open.
  > "whether or not the web app is in the foreground, or even currently loaded, on a user agent"
  > "The service worker will be started as necessary to handle incoming push messages"

### [Notification Triggers - Chrome for Developers](https://developer.chrome.com/docs/web-platform/notification-triggers)

Retrieved 2026-11-27.

- Fact: No web API schedules a notification ahead of time.
  > "The development of Notification Triggers API, part of Google's capabilities project, has ended."
  > "| 5. Launch | Not started |"

### [AlarmKit - Apple Developer Documentation](https://developer.apple.com/documentation/alarmkit)

Retrieved 2026-11-27.

- Fact: iOS apps schedule alarms with AlarmKit.
  > "Schedule prominent alarms and countdowns to help people manage their time."

### [Wake up to the AlarmKit API - WWDC25 - Apple Developer](https://developer.apple.com/videos/play/wwdc2025/230/)

Retrieved 2026-11-27.

- Fact: An AlarmKit alarm rings when the phone is silenced or in a Focus.
  > "When it fires, the alert breaks through the silent mode and the current focus."
- Fact: An AlarmKit alarm button can open the app.
  > "Let’s set up an action using an app intent to open my app when the button is tapped."

### [Schedule alarms - Android Developers](https://developer.android.com/develop/background-work/services/alarms)

Retrieved 2026-11-27.

- Fact: An Android exact alarm fires when the app is not running and the phone is asleep.
  > "They operate outside of your application, so you can use them to trigger events or actions even when your app is not running, and even if the device itself is asleep."
  > "The system invokes an *exact alarm* at a precise moment in the future."
- Fact: An app that targets Android 13 or higher does not get the exact alarm permission at install.
  > "The `SCHEDULE_EXACT_ALARM` permission is not pre-granted to fresh installs of apps targeting Android 13 (API level 33) and higher."
- Fact: The user must grant the exact alarm permission, and the user or the system can revoke it.
  > "Unlike `USE_EXACT_ALARM`, the `SCHEDULE_EXACT_ALARM` permission must be granted by the user."
  > "Both the user and the system can revoke the `SCHEDULE_EXACT_ALARM` permission."
- Fact: The app checks the permission and opens the system settings screen to request it.
  > "To check whether the permission is granted to your app, call `canScheduleExactAlarms()` before trying to set an exact alarm."
  > "Invoke an intent that includes the `ACTION_REQUEST_SCHEDULE_EXACT_ALARM` intent action."
