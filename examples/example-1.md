# CRDT Basics

## TL;DR
CRDTs let independent replicas accept concurrent edits and merge them without coordination — the data structure's math guarantees eventual consistency, so you never need a lock or a tie-breaking server.

## What You Already Knew
- Distributed systems can't avoid network partitions, so you have to pick your poison between consistency and availability (the CAP tradeoff you've read about)
- CRDTs are already in the wild: Riak, Redis, Azure Cosmos, and collaborative tools like Yjs and Automerge all use them
- You've already built something CoSyntax-shaped — a shared editor where multiple users see each other's edits in real time — so you have intuition for why this problem is hard

## What's New Here

The core insight: instead of trying to serialize concurrent edits (OT's approach), CRDTs sidestep the problem by making the data structure itself commutative and idempotent. The order you apply updates doesn't matter, and applying the same update twice is fine. That's the whole trick.

**Two flavors, different tradeoffs.**  
- *Operation-based CRDTs* send small diffs, but depend on reliable delivery and causal ordering (usually vector clocks). Cheap bandwidth-wise, fragile under real network conditions.  
- *State-based CRDTs* send the full replica state. Bulkier, but robust — if a message gets lost, a retransmit still converges correctly. A middle ground: delta-CRDTs, which transmit only the changed portion of state.

**Non-commutativity is the real headache.**  
The BrickSync paper makes this concrete: 3D rotation is non-commutative, so two users rotating the same object in different orders produce different final orientations. There's no clean merge. You either pick last-writer-wins (simple, but one user's intent gets discarded), or you average/blend (more collaborative, but can feel weird). Neither is fully satisfying, which is why the paper proposes "dynamic strategy switching" — the CRDT adapts its conflict resolution mode based on context.

**Operation-based vs. state-based in practice.**  
The BrickSync team started with operation-based, hit the non-commutativity wall, and switched to state-based. Their `MV-Transformer` is a state-based CRDT that tracks position, rotation, and scale — plus a register for which user is currently holding the object. State-based won because it retains enough history to support richer conflict resolution strategies.

**CRDTs vs. OT for collaborative text.**  
OT (what Google Docs uses) transforms operations against each other — compact and efficient, but the transformation rules are notoriously tricky to get right. CRDTs assign each character a unique ID; ordering is by ID, not position, so concurrent inserts at the "same" position resolve deterministically. Downside: deleted characters leave tombstones that accumulate over time.

**Latency numbers from a real P2P + CRDT setup.**  
WebRTC P2P averaged ~50ms vs ~200ms for a remote WebSocket server — roughly a 75% reduction. Best recorded was 18ms P2P vs 45ms on WebSocket. The local-update-first property of CRDTs helps too: users see their own edits instantly, and the network sync catches up in the background.

> check: the paper only tested two users; it's unclear how state-based CRDT bandwidth scales with more replicas and larger payloads.

## Details

- Vector clocks scale linearly with node count — in a large system they become a significant overhead, which is one motivation for delta-CRDTs
- WebRTC caps payload at 16 KB per message; anything bigger needs chunking
- The BrickSync signaling server (for peer discovery) is a WebSocket server that's only involved in the initial handshake — once peers connect, it drops out of the data path
- For text, CRDT IDs typically encode (siteId, counter, timestamp) — deterministic comparison ensures all replicas land on the same ordering independently
- "Global rules" (physics, gravity, enemy spawns) are a CRDT blind spot — there's no author to attribute them to, which breaks the usual CRDT model
- Consumer isolation level matters for Kafka-style systems: `read_committed` filters out in-flight transactions at the consumer

## Connections

**Touches:**
- [[CAP Theorem]] — CRDTs are an explicit design response to the partition problem; eventual consistency is the bet they make
- [[Exactly Once Semantics]] — both tackle the "what happens when messages arrive out of order or twice" problem, just at different layers

**Adjacent:**
- [[Operational Transformation]] — the OT alternative to CRDTs for collaborative editing; worth its own note given how different the mental model is
- [[Vector Clocks]] — CRDTs frequently rely on them for causal ordering; the overhead at scale is a genuine concern
- [[Delta-State CRDTs]] — the bandwidth-efficient middle ground between op-based and full state-based

**Belongs Under:**
- [[Distributed Systems MOC]]
- [[Data Engineering MOC]]

## Open Questions
1. At what replica count does state-based CRDT bandwidth become impractical, and is delta-CRDT the actual answer for multi-user production systems like Yjs?
2. The "dynamic strategy switching" idea in BrickSync sounds promising — is there any formalism for when to switch strategies, or is it purely heuristic?
3. For non-commutative operations like rotation, averaging feels unpredictable. What do production VR collaboration systems actually do here?
4. How does garbage-collecting CRDT tombstones work without coordination? This seems like it reintroduces the synchronization problem CRDTs were designed to avoid.