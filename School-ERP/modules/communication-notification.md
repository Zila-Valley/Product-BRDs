# Communication & Notification Module

## 1. Module Overview
This module acts as the centralized nervous system for all alerts, notices, and messaging between the school administration, teachers, parents, and students.

## 2. Business Purpose
To replace disjointed WhatsApp groups and physical notice boards with a secure, auditable, and automated digital communication platform.

## 3. Users/Roles Involved
- **Institute Admin:** Sends bulk SMS/Emails for holidays or emergencies.
- **Teacher:** Sends class-specific notices or direct messages to parents regarding behavior.
- **System (Automated):** Triggers transactional alerts (e.g., Absenteeism, Fee Receipts).

## 4. Features Implemented
- **Notice Board:** Digital circulars published to the Web and Mobile apps.
- **SMS Gateway Integration:** Plugs into Msg91 to send transactional SMS.
- **Email Gateway Integration:** Plugs into MailGun to send detailed emails (e.g., Report Cards).
- **WhatsApp Integration:** Hooks into WhatsApp Business API for modern messaging.
- **Push Notifications:** Handled by Firebase Cloud Messaging (FCM) on the mobile apps.

## 5. Detected Screens (Web App)
- `Communication > Notice Board`: Form to draft a notice, attach a PDF, and select target audience (All, Specific Class, Teachers Only).
- `Communication > Send SMS/Email`: Bulk messaging interface with templating support.
- `Communication > Message Logs`: Dashboard showing the status of sent messages (Delivered, Failed).

## 6. Backend APIs
- `POST /api/NoticeBoards`
- `POST /api/Communication/send-sms`
- `POST /api/Communication/send-email`
- `GET /api/CommunicationLogs`

## 7. Database Entities
- `NoticeBoards`: (Id, Title, Content, TargetAudience, AttachmentUrl, ExpiryDate)
- `NotificationTemplates`: (Id, EventType, SmsBody, EmailBody)
- `CommunicationLogs`: (Id, RecipientId, MessageType, Content, Status, SentAt)

## 8. Business Rules & Validations
- **Audience Isolation:** Notices published for "Teachers Only" must never appear in the Student/Parent mobile app context.
- **Rate Limiting:** Bulk messaging APIs should ideally throttle requests or utilize background queues to prevent timeout errors and API provider blocking.

## 9. Gaps & Recommendations
- **Gap:** Large bulk message dispatches block the main API thread.
- **Recommendation:** Implement a background worker (e.g., Hangfire or Quartz.NET) to queue and process SMS/Email jobs asynchronously.
- **Gap:** No two-way in-app chat.
- **Recommendation:** Add a secure, monitored 1-on-1 chat feature between Parents and Teachers within the mobile app.

## 10. Test Scenarios
- Create a notice targeting "Class 10 - Section A" and verify it only appears on the dashboards of students in that specific section.
- Trigger an automated event (e.g., marking a student absent) and verify a record is instantly created in `CommunicationLogs` indicating an SMS was queued.
- Attempt to send an SMS with an empty body and verify the API validation rejects it.
