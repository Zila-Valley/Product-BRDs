# Internal Communication Module

## Purpose
To provide a secure, real-time messaging platform for internal team collaboration, replacing third-party apps like WhatsApp or Slack.

## Business Users
- All Users

## Features Implemented
- Hierarchical chat structure: Workspaces -> Channels -> Messages.
- Real-time message delivery via SignalR WebSockets.
- File attachments support.
- Message Reactions (Emojis).
- Mentions (@user).
- Delivery Status tracking (Sent, Delivered, Read).
- Pinned Messages.

## Screens Found in React
- `/communication` -> `MessagingLayout.js`
- `/MyMessages` -> `MyMessages.jsx`

## APIs Found in .NET Core
- `MessagingController.cs`
- `ChannelsController.cs`
- `WorkspacesController.cs`
- `MessageHub.cs` (SignalR)

## Database Entities Used
- `Workspace`
- `Channel`, `ChannelMember`
- `Message`, `MessageAttachment`
- `MessageReaction`, `MessageMention`
- `DeliveryStatus`

## Business Workflow
1. Users are added to Workspaces and Channels based on their department or project.
2. User sends a message via frontend.
3. Backend saves the `Message` and emits it via `MessageHub` to all connected `ChannelMember`s.
4. Clients acknowledge receipt, updating `DeliveryStatus`.

## Validation Rules
- Users can only read/send messages in channels they are members of.
- File attachments have size limits (enforced by IIS/Kestrel limits).

## Role/Permission Rules
- Channel Admins can add/remove members.
- Regular members can only send messages and react.

## Current Gaps
- Pushing attachments via SignalR isn't highly scalable; file uploads should be out-of-band (via standard HTTP POST) and only the URL broadcasted via SignalR.
- Lacks a robust notification system (Push Notifications to mobile apps) when a user is offline.

## Recommended Improvements
- Integrate Firebase Cloud Messaging (FCM) or Apple Push Notification service (APNs) for offline mobile alerts.
- Move older messages to a cold-storage DB (like Cosmos DB) to keep the primary SQL database fast.

## Acceptance Criteria
- Messages appear instantly on recipient screens without page refresh.
- Read receipts update accurately when the recipient opens the channel.

## Test Scenarios
1. **Real-time Delivery**: User A sends message to User B. User B sees message instantly.
2. **Authorization**: User A attempts to query the `/api/messaging` endpoint for a channel they do not belong to -> Should return 403 Forbidden.
