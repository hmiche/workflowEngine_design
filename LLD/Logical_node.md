
## Properties

- **`type: NodeType.LOGICAL`**
  - Indicates the type of the node.

- **`config: { operator: "AND" | "OR"; childNodeIds: string[] }`**
  - Defines the logical operation and the child nodes involved.
    - **`operator`**: Specifies the logical operation (`AND` or `OR`).
    - **`childNodeIds`**: An array of IDs for the child nodes whose states are aggregated.

- **`state: NodeState`**
  - Tracks the current state of the node (`PENDING`, `APPROVED`, `REJECTED`).

---

## Methods

### `evaluate(nodeStates: Record<string, NodeState>): void`
Evaluates the state of the `LogicalNode` based on its operator and the states of its child nodes.

**Parameters:**
- `nodeStates`: A map of node IDs to their states (`PENDING`, `APPROVED`, `REJECTED`).

---

## Example Usage

```typescript
const andNode: LogicalNode = {
  id: "and-node",
  workflowTemplateId: "leave-request-workflow",
  type: NodeType.LOGICAL,
  name: "AND Node",
  description: "Manager and HR approvals are required",
  position: { x: 200, y: 200 },
  createdAt: new Date(),
  updatedAt: new Date(),
  state: NodeState.PENDING,
  config: {
    operator: "AND",
    childNodeIds: ["manager-approval", "hr-approval"],
  },
};

// Simulated child node states
const nodeStates: Record<string, NodeState> = {
  "manager-approval": NodeState.APPROVED,
  "hr-approval": NodeState.APPROVED,
};

// Evaluate the LogicalNode state
andNode.evaluate(nodeStates);
console.log(andNode.state); // Outputs: APPROVED
```