# `BaseNode` Class Documentation

## Overview

The `BaseNode` is an abstract class that serves as the foundation for all node types in the workflow engine. It extends `BaseEntity` and contains common properties and methods used across different nodes.

## Class Definition

```typescript
export abstract class BaseNode extends BaseEntity {
  workflowTemplateId: string;
  type: NodeType;
  name: string;
  description?: string;

  position: Position;

  style?: Record<string, string>;

  createdAt: Date;
  updatedAt: Date;

  parentNodeId?: string;
  childNodeIds?: string[];
}
```

## Properties

- **`workflowTemplateId: string`**
  - Identifier for the workflow template to which this node belongs.

- **`type: NodeType`**
  - The type of the node, defined by the `NodeType` enum (e.g., `START`, `APPROVAL`, `END`).

- **`name: string`**
  - A human-readable name for the node.

- **`description?: string`**
  - An optional description providing additional details about the node.

- **`position: Position`**
  - The coordinates of the node in a two-dimensional space, useful for visual representations.

- **`style?: Record<string, string>`**
  - An optional set of key-value pairs for styling the node (e.g., colors, fonts).

- **`createdAt: Date`**
  - Timestamp indicating when the node was created.

- **`updatedAt: Date`**
  - Timestamp indicating when the node was last updated.

- **`parentNodeId?: string`**
  - An optional identifier for the parent node, if applicable.

- **`childNodeIds?: string[]`**
  - An optional array of identifiers for child nodes, allowing hierarchical relationships.

## Interfaces

### `Position`

Defines the coordinates for a node's position.

```typescript
interface Position {
  x: number;
  y: number;
}
```

- **`x: number`**
  - The horizontal position of the node.

- **`y: number`**
  - The vertical position of the node.

## Usage Example

```typescript
// Example of a concrete node extending BaseNode
class StartNode extends BaseNode {
  constructor() {
    super();
    this.type = NodeType.START;
    this.name = "Start Node";
    this.position = { x: 0, y: 0 };
    this.createdAt = new Date();
    this.updatedAt = new Date();
  }
}
```

## Notes

- The `BaseNode` class is abstract and should be extended by concrete node classes that implement specific behavior.
- The `style` property allows for flexible customization, which can be leveraged by UI components to render the node appropriately.
- The `parentNodeId` and `childNodeIds` properties facilitate building relationships between nodes, essential for constructing workflow hierarchies.

## Enums

### `NodeType`

An enumeration of possible node types.

```typescript
export enum NodeType {
  START = "START",
  APPROVAL = "APPROVAL",
  DOCUMENT = "DOCUMENT",
  NOTIFICATION = "NOTIFICATION",
  CONDITION = "CONDITION",
  END = "END",
}
```

