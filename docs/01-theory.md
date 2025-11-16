| Guarantee | Batch Processing | Stream Processing |
|---|---|---|
| **At-Most-Once** | **"Fire and... Fail?"** The entire batch is processed. If the job fails, it is not retried. The data is lost. | **"Fire and Forget"** An event is processed. If the processor fails, the event is not redelivered. The data is lost. |
| **At-Least-Once** | **"The Full Re-run"** The entire batch is processed. If the job fails, it is re-run from the beginning with the same data. | **"The Checkpoint Retry"** An event is processed. If the processor fails before acknowledging, it restarts from the last checkpoint and re-processes events from that point. |
| **Exactly-Once** | **"The Atomic Commit"** The entire batch's output is written to a staging area and then "committed" in a single transaction. | **"The Distributed Transaction"** The state of the processor is updated and the event is acknowledged in a single, atomic operation (often using two-phase commits or transactional checkpointing). |

**Key Challenge**:
    * **Idempotency at the Job Level.** How do you handle re-running a job that partially succeeded before failing? 
    * **Idempotency at the Event Level.** How do you handle processing the same event multiple times? |