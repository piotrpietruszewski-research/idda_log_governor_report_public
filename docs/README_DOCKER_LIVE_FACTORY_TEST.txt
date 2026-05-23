IDDA Docker Live Factory Test
=============================

Purpose
-------

This document summarizes a long-running Docker live stream test for IDDA Log Governor.

The goal was to check whether IDDA can process a larger live factory-style telemetry stream inside Docker while keeping the output compact, auditable, and decision-oriented.

This was not a static batch benchmark.

This was a live Docker stream processed through IDDA live governor with class-level audit enabled.


Execution mode
--------------

Execution environment:

- Docker container
- live synthetic factory telemetry stream
- IDDA live governor
- class-level JSONL audit
- audit summary generated after the run

Input stream:

- simulated factory telemetry
- repeated machine warning classes
- repeated machine error class
- high-volume INFO background noise
- live window processing


Run overview
------------

Audit file:              docker_live_factory_audit.jsonl
Summary file:            docker_live_factory_summary.txt

Audit windows:           1,500
Input lines:             7,500,000
Retained lines:            750,287
Suppressed lines:        6,749,713
Suppression ratio:          90.00%
Unique audited classes:          3


Severity distribution inside retained classes
---------------------------------------------

WARNING      638,026
ERROR        112,261


Decision distribution inside retained classes
---------------------------------------------

MONITOR_REPEATED         638,026
ESCALATE                 112,261


Top decision classes
--------------------

1. PLC link retry timeout

Count:             337,511
Severity:          WARNING
Decision:          MONITOR_REPEATED

Interpretation:

This class represents repeated retry / timeout behavior on a PLC-link style telemetry channel.

IDDA does not treat each warning as an isolated event.

It collapses the repeated pattern into a monitored decision class.


2. Sensor drift detected

Count:             300,515
Severity:          WARNING
Decision:          MONITOR_REPEATED

Interpretation:

This class represents repeated sensor drift behavior.

IDDA identifies the repeated nature of the warning and keeps it as a monitored operational class.


3. Repeated actuator failure

Count:             112,261
Severity:          ERROR
Decision:          ESCALATE

Interpretation:

This class represents a repeated actuator failure condition.

IDDA assigns escalation semantics because the repeated error class crosses the escalation threshold.


Key result
----------

IDDA reduced:

7,500,000 raw live factory events
→ 750,287 retained decision-relevant events
→ 3 audited decision classes

The important result is not only suppression.

The important result is that the stream remained decision-readable:

- repeated warnings became MONITOR_REPEATED classes,
- repeated errors became ESCALATE classes,
- normal background noise was suppressed,
- the audit remained class-level instead of line-level.


Why suppression is 90% instead of exactly 85%
---------------------------------------------

The live governor was configured with a target suppression setting.

However, IDDA currently works in decision-first mode.

This means:

- it does not keep low-value INFO lines just to hit an exact retained ratio,
- it keeps decision-relevant classes,
- if fewer lines are operationally meaningful, real suppression may be higher than the configured target.

In this run, the effective suppression was 90.00%.

This is expected behavior for decision-first telemetry governance.


Audit model
-----------

The audit is class-level, not full per-line audit.

Each audited class records:

- class ID,
- window count,
- global count,
- severity,
- decision,
- normalized pattern,
- first example line.

This keeps the audit compact while preserving operational meaning.


Operational interpretation
--------------------------

Traditional log processing often treats every log line as a separate storage event.

IDDA treats repeated operational patterns as decision classes.

This means that millions of raw events can be reduced into a small number of operationally meaningful classes.

In this test:

7.5 million raw events were reduced into 3 audited classes.


Positioning
-----------

IDDA Log Governor is not a replacement for observability platforms.

It is a deterministic decision layer that can sit before heavier log storage, alerting, or analytics tools.

Its role is to reduce noisy telemetry into compact, auditable, decision-ready signals.


Important note
--------------

This is a local synthetic Docker live test.

It is not a production guarantee.

A real deployment would still require:

- real ingestion design,
- buffering,
- source tagging,
- retention rules,
- access control,
- monitoring,
- failover,
- production validation,
- integration with existing observability tools.


Conclusion
----------

The Docker live factory test supports the current PoC claim:

IDDA Log Governor can process a multi-million-event live telemetry stream inside Docker, suppress background noise, preserve repeated operational classes, and produce a compact class-level audit.

The result demonstrates stable decision-level compression in a local synthetic environment.
