# MapReduce (Lab1) — Architecture & Review Notes

```mermaid
flowchart TD
    Input[("Input files")]
    Output[("Output files")]

    subgraph COORD["Coordinator"]
        direction LR
        State[("Task state")]
        GT["GetTask"]
        HT["HandinTask"]
        RT["Timeout checker"]
        GT --> State
        HT --> State
        RT --> State
    end

    subgraph MAP["Map tasks (nMap)"]
        direction LR
        M0["Map 0"]
        M1["Map 1"]
        M2["Map 2"]
    end

    subgraph GRID["Intermediate files: mr-&lt;mapIdx&gt;-&lt;bucket&gt;"]
        direction LR
        F00["mr-0-0"]
        F01["mr-0-1"]
        F10["mr-1-0"]
        F11["mr-1-1"]
        F20["mr-2-0"]
        F21["mr-2-1"]
    end

    BARRIER["🚧 PHASE BARRIER 🚧\nfinishedMapCnt == nMap\nNO reduce task is assignable\nbefore every map task completes"]

    subgraph REDUCE["Reduce tasks (nReduce)"]
        direction LR
        R0["Reduce 0"]
        R1["Reduce 1"]
    end

    MAP -.->|"GetTask / HandinTask RPC"| GT
    REDUCE -.->|"GetTask / HandinTask RPC"| GT

    Input --> M0 & M1 & M2

    M0 -->|"fan-out:\nihash(key)%nReduce"| F00
    M0 --> F01
    M1 --> F10
    M1 --> F11
    M2 --> F20
    M2 --> F21

    F00 --> BARRIER
    F10 --> BARRIER
    F20 --> BARRIER
    F01 --> BARRIER
    F11 --> BARRIER
    F21 --> BARRIER

    BARRIER -->|"fan-in:\nreads mr-*-Y"| R0
    BARRIER --> R1

    R0 --> Output
    R1 --> Output

    N1["Completion is implicit: coordinator process\nexits once Done()==true, killing the Unix\nsocket. Next worker RPC fails -> worker exits."]
    N3["A 'timed-out' task may just be slow, not dead:\ntwo workers can race to write the same mr-X-Y.\nSafe only because map is deterministic and\nos.Rename is atomic."]
    N4["Timeout is looser than it looks: expire is set\nto now+MAX_TASK_TIME, then checked again against\nMAX_TASK_TIME, so real timeout is ~2x, ~20s."]
    N5["nReduce must stay identical for every task in a\njob. If it changed between runs reusing old files,\na key could split across mismatched buckets ->\nsilently wrong output, not just an empty bucket."]

    GT -.-> N1
    RT -.-> N4
    M1 -.-> N3
    GRID -.-> N5

    classDef note fill:#fff3b0,stroke:#b59f00,color:#333,text-align:left;
    classDef barrier fill:#f8d7da,stroke:#dc3545,stroke-width:4px,color:#721c24,font-weight:bold;
    class N1,N3,N4,N5 note;
    class BARRIER barrier;
```

The barrier node is the key structural fact: it's not a scheduling nicety,
it's forced by the fan-out/fan-in shape — every map task writes into every
bucket, so every reduce task's fan-in spans *all* map tasks by construction.
"All maps done" and "any reduce task ready" are the same condition.

**Open question (parked, not yet answered):** what happens if a worker
crashes *between* writing `mr-X-Y` to disk and calling `HandinTask`? (Hint:
can the coordinator tell "slow" apart from "dead but already wrote the
file" — and given N3 above, does it need to?)
