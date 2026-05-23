IDDA Profile Comparison — Web/API Stream
=======================================

Purpose
-------

This test checks whether IDDA Log Governor is tied to one specific stream type or whether it can adapt to a different log domain by changing only the normalization profile.

The tested stream was a synthetic Web/API log stream containing:

- INFO requests,
- WARNING rate limit events,
- WARNING slow request events,
- WARNING auth denied events,
- ERROR upstream unavailable events,
- ERROR payment provider failure events.


Profiles tested
---------------

1. default

The default profile preserves endpoint-level detail.

Example:

- slow request on /api/orders
- slow request on /api/payment
- slow request on /metrics
- rate limit on /api/search
- rate limit on /health

Result:

Unique audited classes: 19


2. web_compact

The web_compact profile collapses endpoint-specific details into semantic event classes.

Example:

- slow request
- rate limit exceeded
- auth denied
- upstream unavailable
- payment provider failure

Result:

Unique audited classes: 5


Interpretation
--------------

The same IDDA decision core produced two different levels of operational view:

default profile:
    endpoint-level diagnosis

web_compact profile:
    semantic-level decision overview

This shows that the IDDA core does not need to be rewritten for each stream type.

Instead, domain-specific normalization profiles can control the granularity of the decision classes while the deterministic decision logic remains stable.


Key result
----------

Web/API stream:

default profile      → 19 classes
web_compact profile  → 5 classes

Same core.
Different normalization profile.
Different decision granularity.


Why this matters
----------------

Operational teams may need endpoint-level detail during debugging.

Management or alerting layers may need a compact semantic view.

IDDA can support both by changing the normalization profile, not by changing the core decision logic.


Positioning
-----------

This supports the architectural claim that IDDA is not just a log filter.

It is a deterministic decision layer with configurable interpretation profiles for noisy telemetry streams.
