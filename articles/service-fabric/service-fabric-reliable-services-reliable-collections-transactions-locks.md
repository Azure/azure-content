---
title: Transactions And Lock Modes in Azure Service Fabric Reliable Collections | Microsoft Docs
description: Azure Service Fabric Reliable State Manager and Reliable Collections Transactions and Locking.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak

ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun

---
# Transactions and lock modes in Azure Service Fabric Reliable Collections

## Transaction
A transaction is a sequence of operations performed as a single logical unit of work.
A transaction must exhibit the following ACID properties. (see: https://technet.microsoft.com/en-us/library/ms190612)
* **Atomicity**: A transaction must be an atomic unit of work. In other words, either all its data modifications are performed, or none of them is performed.
* **Consistency**: When completed, a transaction must leave all data in a consistent state. All internal data structures must be correct at the end of the transaction.
* **Isolation**: Modifications made by concurrent transactions must be isolated from the modifications made by any other concurrent transactions. The isolation level used for an operation within an ITransaction is determined by the IReliableState performing the operation.
* **Durability**: After a transaction has completed, its effects are permanently in place in the system. The modifications persist even in the event of a system failure.

### Isolation levels
Isolation level defines the degree to which the transaction must be isolated from modifications made by other transactions.
There are two isolation levels that are supported in Reliable Collections:

* **Repeatable Read**: Specifies that statements cannot read data that has been modified but not yet committed by other transactions and that no other transactions can modify data that has been read by the current transaction until the current transaction finishes. For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snapshot**: Specifies that data read by any statement in a transaction is the transactionally consistent version of the data that existed at the start of the transaction.
  The transaction can recognize only data modifications that were committed before the start of the transaction.
  Data modifications made by other transactions after the start of the current transaction are not visible to statements executing in the current transaction.
  The effect is as if the statements in a transaction get a snapshot of the committed data as it existed at the start of the transaction.
  Snapshots are consistent across Reliable Collections.
  For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Reliable Collections automatically choose the isolation level to use for a given read operation depending on the operation and the role of the replica at the time of transaction's creation.
Following is the table that depicts isolation level defaults for Reliable Dictionary and Queue operations.

| Operation \ Role | Primary | Secondary |
| --- |:--- |:--- |
| Single Entity Read |Repeatable Read |Snapshot |
| Enumeration, Count |Snapshot |Snapshot |

> [!NOTE]
> Common examples for Single Entity Operations are `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Both the Reliable Dictionary and the Reliable Queue support Read Your Writes.
In other words, any write within a transaction will be visible to a following read
that belongs to the same transaction.

## Locks
In Reliable Collections, all transactions implement rigorous two phase locking: a transaction does not release
the locks it has acquired until the transaction terminates with either an abort or a commit.

Reliable Dictionary uses row level locking for all single entity operations.
Reliable Queue trades off concurrency for strict transactional FIFO property.
Reliable Queue uses operation level locks allowing one transaction with `TryPeekAsync` and/or `TryDequeueAsync` and one transaction with `EnqueueAsync` at a time.
Note that to preserve FIFO, if a `TryPeekAsync` or `TryDequeueAsync` ever observes that the Reliable Queue is empty, they will also lock `EnqueueAsync`.

Write operations always take Exclusive locks.
For read operations, the locking depends on a couple of factors.
Any read operation done using Snapshot isolation is lock free.
Any Repeatable Read operation by default takes Shared locks.
However, for any read operation that supports Repeatable Read, the user can ask for an Update lock instead of the Shared lock.
An Update lock is an asymmetric lock used to prevent a common form of deadlock that occurs when multiple transactions lock resources for potential updates at a later time.

The lock compatibility matrix can be found in the following table:

| Request \ Granted | None | Shared | Update | Exclusive |
| --- |:--- |:--- |:--- |:--- |
| Shared |No conflict |No conflict |Conflict |Conflict |
| Update |No conflict |No conflict |Conflict |Conflict |
| Exclusive |No conflict |Conflict |Conflict |Conflict |

Time-out argument in the Reliable Collections APIs is used for deadlock detection.

### Common update lock usage scenario

**Reading and then writing without using an update lock:**
```csharp
 using (ITransaction tx = this.stateManager.CreateTransaction())
 {
  ConditionalValue<int> counter_option = await dict.TryGetValueAsync(tx, counter_id);
  if (counter_option.HasValue)
  {
    await dict.SetAsync(tx, counter_id, counter_option.Value + 1);
  }
  await tx.CommitAsync();
 }
```
With the above code, this is a possible event sequence:

| Event Order | Transaction    | Action 
| ----------- | -------------- |:------ 
| 1           | tx<sub>1</sub> | Takes read lock on counter_id 
| 2           | tx<sub>2</sub> | Takes shared read lock on counter_id 
| 3           | tx<sub>1</sub> | Tries to upgrade read lock to write lock, but has to wait for tx<sub>2</sub> to release lock 
| 4           | tx<sub>2</sub> | Tries to upgrade read lock to write lock, but has to wait for tx<sub>1</sub> to release lock 
|             |                | Deadlock 

**[Correct Usage] Reading and then writing by using an update lock:**
```csharp
 using (ITransaction tx = this.stateManager.CreateTransaction())
 {
  ConditionalValue<int> counter_option = await dict.TryGetValueAsync(tx, counter_id, LockMode.Update);
  if (counter_option.HasValue)
  {
    await dict.SetAsync(tx, counter_id, counter_option.Value + 1);
  }
  await tx.CommitAsync();
 }
```

| Event Order | Transaction    | Action 
| ----------- | -------------- |:------ 
| 1           | tx<sub>1</sub> | Takes upgrade lock on counterid 
| 2           | tx<sub>2</sub> | Tries to take upgrade lock, but has to wait for tx<sub>1</sub> to release lock
| 3           | tx<sub>1</sub> | Upgrades lock to write lock, writes, and releases lock
| 4           | tx<sub>2</sub> | Successfully takes upgrade lock, reads, upgrades lock to write lock, writes, releases lock
|             |                | No deadlock 


## Next steps
* [Working with Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services notifications](service-fabric-reliable-services-notifications.md)
* [Reliable Services backup and restore (disaster recovery)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager configuration](service-fabric-reliable-services-configuration.md)
* [Developer reference for Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

