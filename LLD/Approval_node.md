### Readme for `ApprovalNode`

# `ApprovalNode` Documentation

## Overview

The `ApprovalNode` represents a single approval task within a workflow. It is responsible for managing the approval process for a specific entity, including handling approver roles, deadlines, notifications, and tracking the approval state.

---

## Class Definition

```typescript
import { BaseNode } from "./base-node.entity";
import { NodeType } from "../enums/node.enums";
import { ApproverType, NotificationChannel } from "../enums/approval.enums";

export class ApprovalNode extends BaseNode {
  type: NodeType.APPROVAL = NodeType.APPROVAL;

  config: {
    approvers: Array<{
      id: string;
      type: ApproverType;
      value: string; // User ID, Role ID, or Group ID
      settings: {
        timing: {
          deadlineHours?: number;
          reminderIntervalHours?: number;
        };

        notifications: {
          channels: NotificationChannel[];
          templates: {
            assigned: string;
            reminder: string;
          };
          additionalRecipients?: string[];
        };
      };
    }>;
  };

  state: NodeState = NodeState.PENDING;

  updateState(newState: NodeState): void {
    this.state = newState;
  }
}
```

---

## Properties

- **`type: NodeType.APPROVAL`**
  - Indicates the type of the node.

- **`config: { approvers: Array<...> }`**
  - Configuration for the approval process, including:
    - **Approvers**: Defines the individuals or groups responsible for approving.
    - **Timing**: Specifies deadlines and reminder intervals.
    - **Notifications**: Configures notification channels and templates.

- **`state: NodeState`**
  - Tracks the current state of the node (`PENDING`, `APPROVED`, `REJECTED`).

---

## Methods

### `updateState(newState: NodeState): void`
Updates the node's state to a new value.

**Parameters:**
- `newState`: The new state to assign (`PENDING`, `APPROVED`, `REJECTED`).

---

## Example Usage

```typescript
const managerApprovalNode: ApprovalNode = {
  id: "manager-approval",
  workflowTemplateId: "leave-request-workflow",
  type: NodeType.APPROVAL,
  name: "Manager Approval",
  description: "Manager must approve the leave request",
  position: { x: 100, y: 100 },
  createdAt: new Date(),
  updatedAt: new Date(),
  state: NodeState.PENDING,
  config: {
    approvers: [
      {
        id: "manager-approver",
        type: ApproverType.ROLE,
        value: "LINE_MANAGER",
        settings: {
          timing: {
            deadlineHours: 24,
            reminderIntervalHours: 4,
          },
          notifications: {
            channels: ["EMAIL"],
            templates: {
              assigned: "MANAGER_APPROVAL_ASSIGNED",
              reminder: "MANAGER_APPROVAL_REMINDER",
            },
          },
        },
      },
    ],
  },
};

// Update the node state
managerApprovalNode.updateState(NodeState.APPROVED);
console.log(managerApprovalNode.state); // Outputs: APPROVED
```

---

### Readme for `LogicalNode`

# `LogicalNode` Documentation

## Overview

The `LogicalNode` represents logical operations (`AND`, `OR`) in a workflow. It aggregates the results of child nodes and evaluates its own state based on the configured operator and the states of its child nodes.

---

## Class Definition

```typescript
import { BaseNode } from "./base-node.entity";
import { NodeType } from "../enums/node.enums";

export class LogicalNode extends BaseNode {
  type: NodeType.LOGICAL = NodeType.LOGICAL;

  config: {
    operator: "AND" | "OR"; // Logical operator for aggregating results
    childNodeIds: string[]; // IDs of child nodes to evaluate
  };

  state: NodeState = NodeState.PENDING;

  evaluate(nodeStates: Record<string, NodeState>): void {
    if (this.config.operator === "AND") {
      this.state = this.config.childNodeIds.every(
        (nodeId) => nodeStates[nodeId] === NodeState.APPROVED
      )
        ? NodeState.APPROVED
        : NodeState.PENDING;
    } else if (this.config.operator === "OR") {
      this.state = this.config.childNodeIds.some(
        (nodeId) => nodeStates[nodeId] === NodeState.APPROVED
      )
        ? NodeState.APPROVED
        : NodeState.PENDING;
    }
  }
}
