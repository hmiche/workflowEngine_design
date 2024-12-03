# NotificationNode Documentation

## Overview

The `NotificationNode` is a specialized node in the workflow engine responsible for handling notifications. It triggers alerts, messages, or updates to relevant recipients based on events in the workflow. This node is linked to a parent node (e.g., `ApprovalNode`) and allows configuration of recipients, notification channels, and dynamic content.

---

## Class Definition

```typescript
import { BaseNode } from "./base-node.entity";
import { NodeType } from "../enums/node.enums";
import { NotificationChannel, NotificationPriority, NotificationType } from "../enums/notification.enums";

export class NotificationNode extends BaseNode {
  type: NodeType.NOTIFICATION = NodeType.NOTIFICATION;

  config: {
    parentNodeId: string; // ID of the node that triggers this notification

    template: {
      id: string; // Reference to a notification template
      type: NotificationType; // Type of notification
    };

    recipients: Array<{
      type: "USER" | "ROLE" | "TEAM" | "HIERARCHY";
      value: string; // ID of user, role, team, or hierarchy level
    }>;

    channels: NotificationChannel[];

    settings: {
      priority: NotificationPriority;
      requireAcknowledgment?: boolean;
      retryOnFailure?: boolean;
      maxRetries?: number;
      retryIntervalMinutes?: number;
    };

    dynamicContent?: {
      workflowData: Record<string, string>; // Key-value pairs from workflow data
      includeWorkflowStatus?: boolean;
      includeDocumentLinks?: boolean;
    };
  };
}
```

---

## Properties

### **`type: NodeType.NOTIFICATION`**
Indicates the type of the node as a notification node.

### **`config`**
The configuration object for the `NotificationNode`. It includes the following properties:

#### **`parentNodeId`**
- ID of the parent node that triggers this notification (e.g., `ApprovalNode`).

#### **`template`**
- **`id`**: Reference to the notification template.
- **`type`**: Specifies the type of notification (e.g., `APPROVAL_REQUIRED`, `REQUEST_SUBMITTED`).

#### **`recipients`**
Defines the recipients of the notification:
- **`type`**: The recipient type (`USER`, `ROLE`, `TEAM`, or `HIERARCHY`).
- **`value`**: The ID of the user, role, or team, or the hierarchy level.

#### **`channels`**
Specifies the channels used to send the notification (e.g., `EMAIL`, `IN_APP`).

#### **`settings`**
Additional settings for the notification:
- **`priority`**: The priority of the notification (`LOW`, `MEDIUM`, `HIGH`, `URGENT`).
- **`requireAcknowledgment`**: Whether recipients must acknowledge the notification.
- **`retryOnFailure`**: Whether the system retries on failure.
- **`maxRetries`**: Maximum number of retry attempts.
- **`retryIntervalMinutes`**: Delay between retries in minutes.

#### **`dynamicContent`**
Dynamic content to be included in the notification:
- **`workflowData`**: Key-value pairs mapping workflow fields to notification variables.
- **`includeWorkflowStatus`**: Whether to include the workflow status in the notification.
- **`includeDocumentLinks`**: Whether to include links to related documents.

---

## Example Usage

### Notification for Leave Request Approval

```typescript
const notificationNode: NotificationNode = {
  id: "notify-approval-1",
  workflowTemplateId: "leave-request-workflow",
  type: NodeType.NOTIFICATION,
  name: "Approval Notification",
  description: "Notify relevant parties about leave approval",
  position: { x: 400, y: 200 },
  config: {
    parentNodeId: "manager-approval",

    template: {
      id: "approval-required-template",
      type: NotificationType.APPROVAL_REQUIRED,
    },

    recipients: [
      { type: "HIERARCHY", value: "1" }, // Notify N+1 (Direct Manager)
      { type: "ROLE", value: "HR_RESPONSIBLE" }, // Notify HR responsible
    ],

    channels: [NotificationChannel.EMAIL, NotificationChannel.IN_APP],

    settings: {
      priority: NotificationPriority.HIGH,
      requireAcknowledgment: true,
      retryOnFailure: true,
      maxRetries: 3,
      retryIntervalMinutes: 15,
    },

    dynamicContent: {
      workflowData: {
        leaveType: "type_of_leave",
        startDate: "leave_start",
        endDate: "leave_end",
      },
      includeWorkflowStatus: true,
      includeDocumentLinks: true,
    },
  },
};
```

---

## Usage Flow

1. **Link to Parent Node:**
   - The `NotificationNode` is triggered by the specified `parentNodeId` (e.g., `manager-approval`).

2. **Configure Recipients:**
   - Specify recipients based on user, role, team, or hierarchy.

3. **Define Notification Channels:**
   - Use `EMAIL`, `IN_APP`, or other supported channels for delivery.

4. **Add Dynamic Content:**
   - Include workflow data and additional context dynamically.

5. **Handle Failures:**
   - Configure retry logic to ensure reliable delivery.

---

## Enum Definitions

### `NotificationType`
- `REQUEST_SUBMITTED`
- `APPROVAL_REQUIRED`
- `REQUEST_APPROVED`
- `REQUEST_REJECTED`
- `DOCUMENT_GENERATED`
- `REMINDER`
- `ESCALATION`

### `NotificationChannel`
- `EMAIL`
- `IN_APP`


### `NotificationPriority`
- `LOW`
- `MEDIUM`
- `HIGH`
- `URGENT`

