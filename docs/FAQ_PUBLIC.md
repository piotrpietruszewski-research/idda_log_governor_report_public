# IDDA Log Governor — Public FAQ

## 1. Is IDDA Log Governor just log sampling?

No.

Log sampling usually reduces volume by keeping only a portion of events.

IDDA Log Governor is focused on deterministic reduction into decision classes.

The goal is not simply to keep fewer lines.

The goal is to preserve operational meaning:

- what repeated,
- how often it repeated,
- how severe it was,
- what decision should follow,
- and how that decision can be audited.

## 2. Is this only deduplication?

No.

Deduplication removes repeated identical or near-identical events.

IDDA Log Governor groups repeated operational patterns into decision-relevant classes and maps them to deterministic outcomes.

The output is not only “duplicate removed”.

The output is a compact operational view with severity, decision mapping, and class-level audit.

## 3. Is this only regex matching?

No.

The public report intentionally does not expose implementation details.

At the architectural level, the important point is that normalization profiles control how raw telemetry is interpreted before decision mapping.

The same decision core can produce different levels of operational granularity depending on the normalization profile.

For example:

Detailed API profile:  19 decision classes  
Compact API profile:    5 decision classes

Same core.

Different operational view.

## 4. Does IDDA Log Governor replace observability platforms?

No.

IDDA Log Governor is not positioned as a replacement for observability platforms.

It is a deterministic decision layer that can sit before heavier storage, alerting, or analytics tools.

Its role is to reduce noisy telemetry into compact, auditable, decision-ready signals.

## 5. Why class-level audit instead of full per-line storage?

Because in high-volume telemetry streams, storing every raw line as an equal operational fact can create noise, cost, and cognitive overload.

Class-level audit keeps the operational meaning compact.

A class-level audit can preserve:

- class count,
- global count,
- severity,
- decision,
- normalized event class,
- representative example.

This keeps the audit readable while still preserving decision traceability.

## 6. What does 90% suppression mean?

In the public Docker live industrial telemetry run:

Input lines:          7,500,000  
Retained lines:         750,287  
Suppressed lines:     6,749,713  
Suppression ratio:       90.00%  
Decision classes:             3

This means that the noisy telemetry stream was reduced into a much smaller decision-relevant view.

Suppression does not mean “ignore everything”.

It means low-value repeated noise is prevented from remaining equally loud, while repeated operational classes remain visible and auditable.

## 7. Why was suppression 90% instead of exactly 85%?

IDDA Log Governor works in decision-first mode.

It does not keep low-value lines just to hit a cosmetic target ratio.

If fewer events are operationally meaningful, effective suppression may be higher than the configured target.

The priority is decision quality, not forcing a fixed percentage.

## 8. Why use synthetic data?

Synthetic data allows public demonstration without exposing private logs, customer data, infrastructure details, or sensitive operational patterns.

The public PoC focuses on the architecture and observable behavior:

- deterministic processing,
- reduction into decision classes,
- profile-based granularity,
- class-level audit,
- throughput behavior.

A real pilot would need real telemetry and production validation.

## 9. Why is there no source code in this repository?

Because this is a public-safe artifact repository.

It intentionally excludes:

- source code,
- raw logs,
- JSONL audit files,
- Docker runners,
- internal event patterns,
- class IDs,
- implementation details.

The purpose of this repository is to show the public PoC result without exposing private implementation logic.

## 10. What is the difference between detailed and compact profiles?

A detailed profile preserves more operational granularity.

A compact profile collapses lower-level details into broader semantic classes.

Example from the public API profile comparison:

Detailed profile:  19 decision classes  
Compact profile:    5 decision classes

This demonstrates that the same decision core can support different operational views depending on the normalization profile.

## 11. Is this production-ready?

No.

This is a local synthetic PoC.

The results are not production guarantees.

A production deployment would require:

- ingestion design,
- buffering,
- source tagging,
- retention rules,
- monitoring,
- access control,
- failover,
- integration work,
- production validation.

## 12. What would a real pilot require?

A real pilot would require a controlled telemetry source and clear evaluation criteria.

Minimum pilot scope:

- selected log stream or telemetry source,
- agreed retention and audit requirements,
- baseline storage / alerting comparison,
- target decision classes,
- safe deployment boundary,
- performance measurements,
- false positive / false negative review,
- integration with existing observability tools.

## 13. What is the main claim of this PoC?

The main claim is not that logs can be compressed.

The main claim is that noisy telemetry can be deterministically reduced into compact, auditable, decision-ready classes.

The PoC shows:

Raw telemetry stream  
    ↓  
Normalization profile  
    ↓  
Repeated event classes  
    ↓  
Severity mapping  
    ↓  
Decision mapping  
    ↓  
Class-level audit

## 14. What is the public-safe boundary?

This repository shows the public-facing result only.

It does not expose internal implementation logic.

The public boundary is intentional:

Shown:

- public report,
- public one-pager,
- Docker live summary,
- profile comparison summary,
- local synthetic benchmark result.

Not shown:

- source code,
- raw logs,
- JSONL audits,
- internal patterns,
- class IDs,
- Docker runners,
- implementation-specific profile logic.

## 15. How should this be interpreted?

IDDA Log Governor should be interpreted as a deterministic decision layer for noisy telemetry streams.

It is not a magic compressor.

It is not an observability replacement.

It is not a production guarantee.

It is a proof-of-concept showing how noisy streams can be reduced into compact, auditable operational meaning before heavier downstream storage, alerting, or analytics.
