# Session 13: Streaming Databases

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [13.1 Stream Processing Concepts](#131-stream-processing-concepts)
- [13.2 Stream vs. Batch Processing](#132-stream-vs-batch-processing)
- [13.3 Event Time vs. Processing Time](#133-event-time-vs-processing-time)
- [13.4 Windowing Operations](#134-windowing-operations)
- [13.5 Stateful Stream Processing](#135-stateful-stream-processing)
- [13.6 Stream Processing Frameworks](#136-stream-processing-frameworks)
- [13.7 Exactly-Once Semantics](#137-exactly-once-semantics)

---

## 13.1 Stream Processing Concepts

### What is a Stream?

A **stream** is a continuous, unbounded sequence of events/data records.

**Characteristics:**
- **Unbounded:** No predefined end
- **Continuous:** Data arrives continuously
- **Ordered:** Events have sequence (sometimes)
- **Immutable:** Events cannot change after emission
- **Large scale:** Millions-billions of events per second

### Real-World Streams

| Source | Stream | Event Rate |
|---|---|---|
| **IoT Sensors** | Temperature readings | 1000s per second |
| **Social Media** | Tweets, posts, comments | Millions per second |
| **Financial** | Stock prices, transactions | 100K+ per second |
| **Web Apps** | Clicks, page views, logins | 100K+ per second |
| **System Logs** | Application, system events | 1000s per second |
| **Click Streams** | User interactions | 100K+ per second |

### Stream Processing

**Goal:** Transform, aggregate, enrich streams in real-time

```
Input Stream
    ↓
Process (filter, transform, aggregate)
    ↓
Output Stream (or action)
    ↓
Real-time results
```

**Use cases:**
- Real-time analytics
- Fraud detection
- Anomaly detection
- Real-time dashboards
- Complex event processing
- IoT monitoring

---

## 13.2 Stream vs. Batch Processing

### Batch Processing

**Traditional approach:** Collect data, process periodically

```
Collect Data (1 hour)
    ↓
  Run Job
    ↓
Get Results (1+ hour later)
```

**Characteristics:**
- Periodic execution
- Large data at once
- Higher latency (acceptable)
- Lower infrastructure cost
- Complex to implement real-time

**Example:** Daily sales report

### Stream Processing

**Modern approach:** Process data as it arrives

```
Event arrives
    ↓
Process immediately
    ↓
Result within ms
```

**Characteristics:**
- Continuous execution
- Small data chunks
- Lower latency (critical)
- Higher infrastructure cost
- Enables real-time decisions

**Example:** Fraud detection (block suspicious card)

### Comparison

| Aspect | Batch | Stream |
|---|---|---|
| **Data scope** | Fixed dataset | Unbounded stream |
| **Latency** | Hours | Seconds/milliseconds |
| **Complexity** | Lower | Higher |
| **Cost** | Lower | Higher |
| **Use case** | Analytics | Real-time operations |
| **Implementation** | MapReduce, Spark | Kafka, Flink, Spark |

### Lambda Architecture (Batch + Stream)

Combine batch and stream for comprehensive coverage:

```
                 Input Stream
                      ↓
         ┌────────────┴─────────────┐
         ↓                          ↓
    Speed Layer            Batch Layer
  (Stream Processor)    (Batch Processor)
         ↓                          ↓
      Cache          Historical Results
         │                    │
         └────────────┬───────┘
                      ↓
                  Serving Layer
                      ↓
                    Results
```

---

## 13.3 Event Time vs. Processing Time

### Event Time

**Event time** = when event actually occurred (in real world)

```
Event: User clicks button at 10:15:23.456 UTC
Event time: 10:15:23.456 UTC
```

### Processing Time

**Processing time** = when event processed by system

```
Event: User clicks button at 10:15:23.456 UTC
Arrives at processor: 10:15:25.100 UTC  (1.6 second delay)
Processing time: 10:15:25.100 UTC
```

### Processing Delay

**Skew** = Processing time - Event time

```
Perfect (no delay):  Skew = 0
Network delay:       Skew = 1.6 seconds
Queued processing:   Skew = 10 seconds
Late arrival:        Skew = hours/days
```

### Why Distinguish?

**Problem:** Processing delay varies

```
Events A, B, C occur at:  10:15:00, 10:15:01, 10:15:02
Arrive at processor at:   10:15:02, 10:15:00, 10:15:03

If using processing time:  Out of order!
If using event time:       Correct order
```

### Processing-Time Challenges

**Late-arriving events:**
- Event occurred at 10:15:00
- Arrives for processing at 10:16:00 (60 seconds late)
- Already aggregated previous second?
- Need to update/recalculate

**Solutions:**
- Event time-based windowing
- Allowed lateness parameter
- Result retraction
- Early/late results

---

## 13.4 Windowing Operations

### Why Windowing?

Streams are unbounded. Need to slice into finite chunks for aggregation.

```
Stream: [A, B, C, D, E, F, G, H, I, J, ...]

Sum(all) = infinity (unbounded)

Windows:
- [A, B, C] → 3 items
- [D, E, F] → 3 items
- [G, H, I] → 3 items
```

### Tumbling Window (Fixed Window)

**Non-overlapping fixed-size windows:**

```
Time: 1  2  3  4  5  6  7  8  9  10
Data: A  B  C  D  E  F  G  H  I  J

Windows (size 3):
[A, B, C]        [D, E, F]        [G, H, I]        [J]
```

**Use cases:**
- Hourly traffic reports
- Daily sales totals
- Minute-by-minute metrics

**Example:**
```python
# Sum events in 1-minute windows
stream.window(size=60, slide=0).sum()
```

### Sliding Window (Overlapping)

**Overlapping windows with slide amount:**

```
Time: 1  2  3  4  5  6  7  8  9  10
Data: A  B  C  D  E  F  G  H  I  J

Windows (size 3, slide 1):
   [A, B, C]
      [B, C, D]
         [C, D, E]
            [D, E, F]
               ...
```

**Use cases:**
- Moving averages
- Trend detection
- Real-time smoothing

**Example:**
```python
# 5-minute average with 1-minute slide
stream.window(size=300, slide=60).average()
```

### Session Window

**Window defined by activity gap:**

```
Events: [A, B, C] 2min gap [D, E] 5min gap [F, G, H]

Session 1: [A, B, C]      (events within 2 min)
Session 2: [D, E]         (events within 5 min)
Session 3: [F, G, H]      (events within 2 min)
```

**Use cases:**
- User sessions (active until inactivity)
- Phone call duration
- Maintenance events

**Parameters:**
- Activity timeout: Closes session if no events for duration

---

## 13.5 Stateful Stream Processing

### Stateless Operations

**Operations independent of history:**

```
Input events processed independently
No memory of previous events

Examples:
- Filter: Keep events matching condition
- Map: Transform each event
- Flatmap: One-to-many transformation
```

### Stateful Operations

**Operations dependent on history/context:**

```
Maintain state across events
Examples:
- Aggregation: Sum, count, average over window
- Join: Correlate events from multiple streams
- Deduplication: Track seen IDs to filter duplicates
```

### State Management

**Problem:** System might fail, lose memory

```
Processing state in memory
    ↓
System crashes
    ↓
Lost state → incorrect results
```

**Solution:** Checkpoint state periodically

```
Process stream
    ↓
Accumulate state in memory
    ↓
Every N seconds: Save state to disk/database
    ↓
If crash: Restore from checkpoint
    ↓
Continue processing from checkpoint
```

### Backpressure

**Problem:** Producer faster than consumer

```
Events arrive at 100K/sec
Processing only handles 50K/sec

Queue grows unbounded
    ↓
Memory exhausted
    ↓
System crashes
```

**Solution:** Backpressure mechanism

```
Consumer signals: "Slow down, I'm overwhelmed"
Producer reduces rate
System stabilizes
```

---

## 13.6 Stream Processing Frameworks

### Apache Kafka

**Message broker for streams:**

```
Producers
    ↓
Kafka Cluster (topics, partitions)
    ↓
Consumers
```

**Characteristics:**
- Pub-sub messaging
- Distributed, fault-tolerant
- Persistent storage
- Scales to millions events/sec

**Use:** Stream ingestion, decoupling systems

### Apache Flink

**Stateful stream processing:**

```
Data sources
    ↓
Transformations (map, filter, aggregate)
    ↓
Stateful operations (windows, joins)
    ↓
Sinks (write to storage)
```

**Features:**
- Event-time semantics
- Complex event processing
- State management with checkpoints
- Exactly-once processing

**Example:**
```python
# Count events per session
env = StreamExecutionEnvironment.get_execution_environment()

events = env.add_source(...)
windowed = events.key_by(lambda x: x['user_id']) \
    .window(SessionWindow(30 * 1000)) \
    .process(CountWindowFunction())

windowed.print()
env.execute()
```

### Apache Spark Streaming

**Micro-batching approach:**

```
Collect events for X ms
    ↓
Process batch using Spark
    ↓
Output results
    ↓
Repeat
```

**Characteristics:**
- Uses Spark SQL for processing
- Familiar DataFrame API
- Integrates with batch Spark
- Lower latency than batch

### Kafka Streams

**Light-weight stream processing:**

```
Kafka topic
    ↓
Processing topology (transformations)
    ↓
Kafka topic
```

**Characteristics:**
- Library, not framework
- Simple to deploy
- Runs in application code
- Lower operational complexity

---

## 13.7 Exactly-Once Semantics

### Delivery Guarantees

**At-most-once:** Events processed 0 or 1 times
- **Risk:** Data loss
- **Benefit:** Fast, simple
- **Use case:** Acceptable loss (metrics, logs)

**At-least-once:** Events processed 1+ times
- **Risk:** Duplicates
- **Benefit:** No data loss
- **Use case:** Tolerates duplicates or deduplication

**Exactly-once:** Events processed exactly 1 time
- **Guarantee:** No loss, no duplicates
- **Cost:** Complex, slower
- **Use case:** Financial, billing

### Achieving Exactly-Once

**Idempotent operations:**

```
Operation doesn't depend on how many times executed
Examples:
- INSERT UNIQUE key (second insert ignored)
- SET counter = 10 (always same result)
- DELETE WHERE id = 5 (idempotent)

Non-idempotent:
- INCREMENT counter (depends on run count)
- APPEND "event occurred" (duplicates)
```

**Deduplication:**

```
Event ID: 12345
Processing:
- If ID exists: Skip
- If new: Process and record ID
```

**Transactional writes:**

```
Atomically:
1. Record event processed
2. Write results
3. Update state

All or nothing - no partial state
```

### Implementation Approaches

**Kafka + Transactions:**
```
Read from Kafka (offset tracking)
Process event
Write result + store offset
All in transaction (atomic)
```

**Idempotent sinks:**
```
State stores result with unique key
Reprocessing same event: Overwrites same key
Result same as single processing
```

**Checkpoint-based:**
```
Process events
Checkpoint: state + source offsets
If failure: Revert to last checkpoint
Resume from checkpoint (exactly-once due to atomicity)
```

---

## Practical Stream Processing Example

**Real-time fraud detection:**

```python
from pyflink.datastream import StreamExecutionEnvironment
from pyflink.datastream.functions import MapFunction, FilterFunction

env = StreamExecutionEnvironment.get_execution_environment()

# Source: Kafka transactions
transactions = env.add_source(KafkaSource(...))

# Filter: Potential fraud (high amount or unusual pattern)
def fraud_scorer(transaction):
    score = 0
    if transaction['amount'] > 5000:
        score += 50
    if transaction['country'] != transaction['user_country']:
        score += 40
    if transaction['time_since_last'] < 1:  # Minute
        score += 20
    return score

fraud_scores = transactions.map(lambda t: (t, fraud_scorer(t)))

# Flag high-score transactions
suspicious = fraud_scores.filter(lambda x: x[1] > 70)

# Alert
suspicious.print()

# Also write to database for investigation
suspicious.add_sink(write_to_database)

env.execute("Fraud Detection")
```

---

## Summary

Stream processing enables real-time data operations:

1. **Stream concepts** - Unbounded, continuous sequences of events
2. **Stream vs. batch** - Different trade-offs for latency and simplicity
3. **Event time vs. processing time** - Critical for correct aggregation
4. **Windowing** - Slicing unbounded streams into finite chunks
5. **Stateful processing** - Maintaining context across events
6. **Frameworks** - Kafka, Flink, Spark, Kafka Streams
7. **Exactly-once semantics** - Guaranteeing no loss and no duplicates

Stream processing is essential for modern applications requiring real-time insights and immediate responses to events.
