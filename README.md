# Dependency Graph

This package contains a simple and modern dependency graph data structure for TypeScript.

```
npm install @baileyherbert/dependency-graph
```

---

- [Example](#example)
- [Constructor](#constructor)
- [Methods](#methods)
	- [`addNode(node, [data])`](#addnodenode-data)
	- [`removeNode(node)`](#removenodenode)
	- [`hasNode(node)`](#hasnodenode)
	- [`getNodeData(node)`](#getnodedatanode)
	- [`setNodeData(node, [data])`](#setnodedatanode-data)
	- [`hasNodeData(node)`](#hasnodedatanode)
	- [`addDependency(from, to)`](#adddependencyfrom-to)
	- [`removeDependency(from, to)`](#removedependencyfrom-to)
	- [`getDirectDependenciesOf(node)`](#getdirectdependenciesofnode)
	- [`getDirectDependentsOf(node)`](#getdirectdependentsofnode)
	- [`getDependenciesOf(node, [leaves])`](#getdependenciesofnode-leaves)
	- [`getDependentsOf(node, [leaves])`](#getdependentsofnode-leaves)
	- [`getOverallOrder([leaves])`](#getoverallorderleaves)
	- [`getEntryNodes()`](#getentrynodes)
	- [`clone()`](#clone)
	- [`clear()`](#clear)
- [Properties](#properties)
	- [`allowCircularDependencies`](#allowcirculardependencies)
	- [`size`](#size)
- [Circular dependencies](#circular-dependencies)

---

## Example

```ts
import { DependencyGraph } from '@baileyherbert/dependency-graph';

const graph = new DependencyGraph<string>();

graph.addNode('a');
graph.addNode('b');
graph.addNode('c');

graph.addDependency('a', 'b');
graph.addDependency('b', 'c');

graph.getDependenciesOf('a');  // ['c', 'b']
graph.getDependenciesOf('b');  // ['c']

graph.getDependentsOf('c');    // ['a', 'b']

graph.getOverallOrder();       // ['c', 'b', 'a']
graph.getOverallOrder(true);   // ['c']

graph.addNode('d', 'data');
graph.setNodeData('c', 'more data');

graph.getNodeData('d');        // 'data'
graph.getNodeData('c');        // 'more data'
graph.getNodeData('b');        // undefined
```

---

## Constructor

```ts
new DependencyGraph<T>()
new DependencyGraph<T, D>()
```

- `<T>` is the type to use for nodes.
- `<D>` is the type to use for node data. Defaults to `any`.

## Methods

### `addNode(node, [data])`
Adds a node to the dependency graph.

**Returns:** `void`\
**Parameters:**
- `node: T` – The node to add.
- `data?: D` – Optional data to associate with this node.

### `removeNode(node)`
Removes a node from the dependency graph.

**Returns:** `void`\
**Parameters:**
- `node: T` – The node to remove.

### `hasNode(node)`
Returns true if the specified node exists in the graph.

**Returns:** `boolean`\
**Parameters:**
- `node: T` – The node to check for.

### `getNodeData(node)`
Returns the data for the specified node or `undefined` if not available.

**Returns:** `D | undefined`\
**Parameters:**
- `node: T` – The node to retrieve data for.

### `setNodeData(node, [data])`
Sets the data for the specified node. Setting the data to `undefined` will remove it.

**Returns:** `void`\
**Parameters:**
- `node: T` – The node to set data for.
- `data?: D` – Optional data to associate with this node.

### `hasNodeData(node)`
Returns true if the node has associated data.

**Returns:** `boolean`\
**Parameters:**
- `node: T` – The node to check for.

### `addDependency(from, to)`
Registers that the `from` node depends on the `to` node.

**Returns:** `void`\
**Parameters:**
- `from: T` – The dependent node.
- `to: T` – The dependency node.

### `removeDependency(from, to)`
Removes a dependency between two nodes. The given `from` node will no longer be dependent upon the `to` node.

**Returns:** `void`\
**Parameters:**
- `from: T` – The dependent node.
- `to: T` – The dependency node.

### `getDirectDependenciesOf(node)`
Returns an array containing all direct dependencies of the given node.

**Returns:** `T[]`\
**Parameters:**
- `node: T` – The node to check for.

### `getDirectDependentsOf(node)`
Returns an array containing the nodes that directly depend on the given node.

**Returns:** `T[]`\
**Parameters:**
- `node: T` – The node to check for.

### `getDependenciesOf(node, [leaves])`
Returns an array containing the nodes that the specified node depends on (transitively).

**Returns:** `T[]`\
**Parameters:**
- `node: T` – The node to check for.
- `leaves: boolean` – Only return leaves. Defaults to `false`.

### `getDependentsOf(node, [leaves])`
Returns an array containing the nodes that depend on the specified node (transitively).

**Returns:** `T[]`\
**Parameters:**
- `node: T` – The node to check for.
- `leaves: boolean` – Only return leaves. Defaults to `false`.

### `getOverallOrder([leaves])`
Computes the overall processing order for the dependency graph. Throws an error if circular dependencies are detected.

**Returns:** `T[]`\
**Parameters:**
- `leaves: boolean` – Only return leaves. Defaults to `false`.

### `getEntryNodes()`
Returns an array of nodes that have no dependents.

**Returns:** `T[]`

### `clone()`
Creates a copy of the dependency graph.

**Returns:** `DependencyGraph<T, D>`

### `clear()`
Clears all nodes and data from the dependency graph.

**Returns:** `void`

## Properties

### `allowCircularDependencies`
Whether or not circular dependencies are allowed. Defaults to `false` in which case errors will be thrown upon their detection.

**Type:** `boolean`\
**Default:** `false`

### `size`
The number of nodes in the graph.

**Type:** `number` (read-only)

---

## Circular dependencies

When circular dependencies occur and `allowCircularDependencies` is false, an error will be thrown. You can catch this error and use the information within it to determine which nodes are responsible.

```ts
import { CircularDependencyError } from '@baileyherbert/dependency-graph';

try {
	// Implement graph with a circular dependency
	graph.addDependency('a', 'b');
	graph.addDependency('b', 'c');
	graph.addDependency('c', 'a');

	// The error will be thrown when running computations
	graph.getOverallOrder();
}
catch (error) {
	if (error instanceof CircularDependencyError) {
		error.path;     // ['a', 'b', 'c', 'a']
		error.node;     // 'a'
		error.message;  // Detected circular dependencies (a -> b -> c -> a)
	}
}
```
