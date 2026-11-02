# MongoDB Aggregation Framework and Advanced Patterns

> BITS Pilani — **SSZG507: Modern Database Systems**
>
> **Reference:** Class MongoDB Query Examples

---

## Table of Contents

- [1. MongoDB Data Model](#1-mongodb-data-model)
- [2. Basic Document Operations](#2-basic-document-operations)
- [3. CRUD Operations](#3-crud-operations)
- [4. Querying Documents](#4-querying-documents)
- [5. Query Operators](#5-query-operators)
- [6. Update Operations](#6-update-operations)
- [7. Aggregation Framework](#7-aggregation-framework)
- [8. Advanced Patterns](#8-advanced-patterns)

---

## 1. MongoDB Data Model

### Collections and Documents

MongoDB stores data as **documents** (JSON-like objects) organized in **collections** (similar to tables).

**Advantages over relational databases:**
- Flexible schema: Documents in same collection can have different fields
- Nested data: Support for embedded documents and arrays
- No predefined structure: Add new fields without schema migration

### Document Structure

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "item": "canvas",
  "qty": 100,
  "tags": ["cotton"],
  "size": {
    "h": 28,
    "w": 35.5,
    "uom": "cm"
  }
}
```

### Collection Creation

```javascript
// Create collection with options
db.createCollection("mycol", { 
  capped: true,           // Fixed-size collection
  autoIndexId: true,      // Auto-index _id
  size: 6142800,          // Max size in bytes
  max: 10000              // Max documents
});
```

---

## 2. Basic Document Operations

### insertOne - Insert Single Document

```javascript
db.inventory.insertOne(
  {
    item: "canvas",
    qty: 100,
    tags: ["cotton"],
    size: { h: 28, w: 35.5, uom: "cm" }
  }
);
```

**Result:** Returns `{ acknowledged: true, insertedId: ObjectId(...) }`

### insertMany - Insert Multiple Documents

```javascript
db.inventory.insertMany([
  {
    item: "journal",
    qty: 25,
    tags: ["blank", "red"],
    size: { h: 14, w: 21, uom: "cm" }
  },
  {
    item: "mat",
    qty: 85,
    tags: ["gray"],
    size: { h: 27.9, w: 35.5, uom: "cm" }
  },
  {
    item: "mousepad",
    qty: 25,
    tags: ["gel", "blue"],
    size: { h: 19, w: 22.85, uom: "cm" }
  }
]);
```

### Document with Status Field

```javascript
db.inventory.insertMany([
  { item: "journal", qty: 25, status: "A" },
  { item: "notebook", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
  { item: "paper", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "D" },
  { item: "Planner", qty: 85, status: "A" },
  { item: "Postcard", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "D" }
]);
```

---

## 3. CRUD Operations

### find() - Read Documents

```javascript
// All documents
db.inventory.find({});

// Single document matching filter
db.inventory.findOne({ status: "D" });

// Returns cursor: iterate through results
db.inventory.find({ status: "A" }).pretty();
```

### updateOne - Update Single Document

```javascript
db.inventory.updateOne(
  { item: "paper" },                    // Filter
  {
    $set: {                             // Update operator
      "size.uom": "cm",
      status: "P"
    },
    $currentDate: { lastModified: true }  // Add timestamp
  }
);
```

**Result:** `{ acknowledged: true, matchedCount: 1, modifiedCount: 1 }`

### updateMany - Update Multiple Documents

```javascript
db.inventory.updateMany(
  { "qty": { $lt: 50 } },              // Filter: qty < 50
  {
    $set: {
      "size.uom": "in",
      status: "P"
    },
    $currentDate: { lastModified: true }
  }
);
```

### deleteOne - Delete Single Document

```javascript
db.inventory.deleteOne({ item: "paper" });
// Removes first matching document
```

### deleteMany - Delete Multiple Documents

```javascript
db.inventory.deleteMany({ "qty": { $lt: 50 } });
// Removes all documents where qty < 50
```

---

## 4. Querying Documents

### Simple Equality Query

```javascript
// All documents with status "D"
db.inventory.find({ status: "D" });

// Returns: All documents where status field equals "D"
```

### Multiple Conditions (AND)

```javascript
// status = "A" AND item = "notebook"
db.inventory.find({ status: "A", item: "notebook" });

// Implicit AND: all conditions must match
```

### Multiple Conditions (OR)

```javascript
// status = "A" OR qty < 30
db.inventory.find({
  $or: [
    { status: "A" },
    { qty: { $lt: 30 } }
  ]
});
```

### Complex Query

```javascript
// status = "A" AND (item starts with P OR qty < 30)
db.inventory.find({
  status: "A",
  $or: [
    { item: /^P/ },        // Regex: starts with P
    { qty: { $lt: 30 } }
  ]
});
```

---

## 5. Query Operators

### Comparison Operators

```javascript
// Less than
db.inventory.find({ qty: { $lt: 30 } });

// Greater than
db.inventory.find({ qty: { $gt: 50 } });

// Less than or equal
db.inventory.find({ qty: { $lte: 50 } });

// Greater than or equal
db.inventory.find({ qty: { $gte: 50 } });

// Not equal
db.inventory.find({ status: { $ne: "D" } });

// In set
db.inventory.find({ status: { $in: ["A", "P"] } });

// Not in set
db.inventory.find({ status: { $nin: ["A", "P"] } });
```

### Logical Operators

```javascript
// AND (all conditions)
db.inventory.find({
  $and: [
    { qty: { $lt: 30 } },
    { status: "A" }
  ]
});

// OR (any condition)
db.inventory.find({
  $or: [
    { qty: { $lt: 30 } },
    { status: "A" }
  ]
});

// NOT
db.inventory.find({ qty: { $not: { $lt: 50 } } });

// NOR (none match)
db.inventory.find({
  $nor: [
    { qty: { $lt: 30 } },
    { status: "A" }
  ]
});
```

### Array Operators

```javascript
// Array contains value
db.inventory.find({ tags: "cotton" });

// Array matches all values
db.inventory.find({ tags: { $all: ["blank", "red"] } });

// Array element count
db.inventory.find({ tags: { $size: 2 } });

// Specific array index
db.inventory.find({ "tags.0": "cotton" } );
```

---

## 6. Update Operations

### Update Operators

**$set:** Set field value
```javascript
db.inventory.updateOne(
  { item: "paper" },
  { $set: { status: "P", qty: 100 } }
);
```

**$unset:** Remove field
```javascript
db.inventory.updateOne(
  { item: "paper" },
  { $unset: { status: "" } }
);
```

**$inc:** Increment value
```javascript
db.inventory.updateOne(
  { item: "paper" },
  { $inc: { qty: 5 } }  // Add 5 to qty
);
```

**$push:** Add element to array
```javascript
db.inventory.updateOne(
  { item: "paper" },
  { $push: { tags: "new-tag" } }
);
```

**$currentDate:** Set to current timestamp
```javascript
db.inventory.updateOne(
  { item: "paper" },
  { $currentDate: { lastModified: true } }
);
```

### Nested Document Updates

```javascript
// Update nested field
db.inventory.updateOne(
  { item: "paper" },
  { $set: { "size.uom": "cm" } }
);
```

---

## 7. Aggregation Framework

### Pipeline Concept

Aggregation processes documents through a **pipeline** of stages:

```
Input Documents → [$match] → [$group] → [$project] → Output
```

Each stage transforms data and passes results to next stage.

### Basic Aggregation Example

```javascript
// Group by status, count documents
db.inventory.aggregate([
  {
    $group: {
      _id: "$status",              // Group by status field
      totalCount: { $sum: 1 }      // Count documents
    }
  }
]);
```

**Result:**
```
{ "_id": "A", "totalCount": 4 }
{ "_id": "D", "totalCount": 2 }
```

### $match Stage (Filtering)

```javascript
// Filter: only status = "A"
db.inventory.aggregate([
  {
    $match: { status: "A" }
  }
]);

// Filter: qty > 50
db.inventory.aggregate([
  {
    $match: { qty: { $gt: 50 } }
  }
]);
```

### $group Stage (Aggregation)

```javascript
// Group by status, calculate multiple metrics
db.inventory.aggregate([
  {
    $group: {
      _id: "$status",
      count: { $sum: 1 },
      totalQty: { $sum: "$qty" },
      avgQty: { $avg: "$qty" },
      maxQty: { $max: "$qty" },
      minQty: { $min: "$qty" }
    }
  }
]);
```

### $project Stage (Shape Output)

```javascript
// Include/exclude fields
db.inventory.aggregate([
  {
    $project: {
      item: 1,           // Include
      qty: 1,
      _id: 0             // Exclude _id
    }
  }
]);

// Rename fields
db.inventory.aggregate([
  {
    $project: {
      product: "$item",  // Rename item to product
      quantity: "$qty"   // Rename qty to quantity
    }
  }
]);

// Computed fields
db.inventory.aggregate([
  {
    $project: {
      item: 1,
      qty: 1,
      totalValue: { $multiply: ["$qty", 10] }  // qty * 10
    }
  }
]);
```

### $sort Stage (Ordering)

```javascript
// Sort by qty ascending
db.inventory.aggregate([
  {
    $sort: { qty: 1 }  // 1 = ascending, -1 = descending
  }
]);

// Sort by multiple fields
db.inventory.aggregate([
  {
    $sort: { status: 1, qty: -1 }  // status asc, qty desc
  }
]);
```

### $limit and $skip Stages (Pagination)

```javascript
// Get first 5 documents
db.inventory.aggregate([
  { $limit: 5 }
]);

// Skip first 10, take next 5
db.inventory.aggregate([
  { $skip: 10 },
  { $limit: 5 }
]);
```

### Complex Pipeline Example

```javascript
// Find high-value items by status
db.inventory.aggregate([
  // Stage 1: Filter
  { $match: { qty: { $gt: 25 } } },
  
  // Stage 2: Group and aggregate
  {
    $group: {
      _id: "$status",
      count: { $sum: 1 },
      avgQty: { $avg: "$qty" }
    }
  },
  
  // Stage 3: Sort
  { $sort: { count: -1 } },
  
  // Stage 4: Project output
  {
    $project: {
      status: "$_id",
      documentCount: "$count",
      averageQuantity: "$avgQty",
      _id: 0
    }
  }
]);
```

---

## 8. Advanced Patterns

### $unwind Stage (Flatten Arrays)

```javascript
// Flatten tags array
db.inventory.aggregate([
  { $unwind: "$tags" },  // Create document per tag
  {
    $group: {
      _id: "$tags",
      count: { $sum: 1 }
    }
  }
]);
// Counts documents per tag value
```

### $lookup Stage (JOIN Operation)

```javascript
// Join with another collection
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customerId",
      foreignField: "_id",
      as: "customerInfo"
    }
  }
]);
```

### Aggregation Expressions

```javascript
// Math operations
db.inventory.aggregate([
  {
    $group: {
      _id: "$status",
      totalValue: {
        $sum: { $multiply: ["$qty", "$price"] }
      }
    }
  }
]);

// String operations
db.inventory.aggregate([
  {
    $project: {
      upperItem: { $toUpper: "$item" },
      itemLength: { $strLenCP: "$item" }
    }
  }
]);

// Conditional logic
db.inventory.aggregate([
  {
    $project: {
      item: 1,
      qty: 1,
      category: {
        $cond: [
          { $gte: ["$qty", 50] },
          "High Stock",
          "Low Stock"
        ]
      }
    }
  }
]);
```

### Performance Tips

1. **$match early:** Filter before grouping
2. **$project to limit fields:** Reduce data pipeline
3. **$limit after $sort:** Get top N efficiently
4. **Use indexes:** Indexed fields speed up $match
5. **Avoid $lookup if possible:** Expensive operation

---

## Summary

MongoDB provides powerful querying and aggregation capabilities:
- **Flexible documents:** Schema-less storage
- **Rich query language:** Complex filtering and operators
- **Aggregation pipeline:** Multi-stage data processing
- **Scalability:** Designed for distributed systems

Understanding these patterns enables building efficient MongoDB applications for various use cases.

