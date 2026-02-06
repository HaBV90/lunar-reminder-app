# ADR-006: Push Notifications

**Date:** 2026-02-02
**Status:** Approved

## Context

Push notifications are a core feature of the Lunar Reminder App. Users need to receive timely reminders for death anniversaries (gio), birthdays, and other lunar calendar events, even when the app is not running.

## Decision

We chose **OneSignal** as the push notification service.

## Rationale

- **Free tier generous:** Supports up to 10,000 subscribers on free plan, sufficient for MVP and initial growth
- **Easy integration:** Simple SDK for React Native with good documentation
- **Supports iOS & Android:** Single integration handles both platforms
- **Rich features:** Supports scheduling, segmentation, rich notifications, and in-app messaging
- **Analytics:** Built-in delivery and engagement tracking

## Alternatives Considered

### Firebase Cloud Messaging (FCM)
- **Pros:** Free, Google-backed, widely used
- **Cons:** More complex setup, requires separate implementation for iOS (APNs), more configuration needed

### Custom Solution (APNs + FCM directly)
- **Pros:** Full control, no third-party dependency
- **Cons:** Significantly more development effort, need to handle both platforms separately, queue management complexity

### Amazon SNS
- **Pros:** AWS integration, scalable
- **Cons:** More complex pricing, enterprise-focused, more setup required

### Pusher Beams
- **Pros:** Simple API, good documentation
- **Cons:** Smaller free tier, less feature-rich than OneSignal

## Consequences

### Positive
- Rapid implementation with well-documented SDK
- Built-in notification scheduling for lunar event reminders
- User segmentation for targeted notifications
- Free tier covers early growth phase
- Easy A/B testing for notification content

### Negative
- Third-party dependency for critical feature
- Data stored on external service
- May need to migrate if usage exceeds free tier significantly

### Mitigations
- Abstract notification logic to allow provider switching
- Monitor usage metrics proactively
- Plan migration path to FCM or custom solution for scale
- Store notification preferences locally as backup
