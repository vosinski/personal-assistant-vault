#project 
We analyzed two final architecture options:
Option 1 (Synchronous Model API) : Atlas calls the model directly and waits for a real-time response (under one second). The API can send back both the score and the segment immediately. This approach is designed for instant decisions like Autobet, where timing is critical.
Option 2 (Asynchronous Pub/Sub → P360): Events are sent through Pub/Sub workers, and the model updates P360 with the new data. Atlas then reads these updates. The cycle takes about 2–3 minutes and is suitable for cases where small delays are acceptable.

Option 1 — Sync Model API (Atlas <- >Model)

Pros (from discussion):

Enables true real-time decisions where latency is critical (e.g., Autobet on entering a slot). 

Bypasses P360 refresh cadence; not constrained by “refresh at init only.” 

API can return both score and segment, so Atlas/Game can act immediately without P360 lookups. 

Future-proof: More sync use cases; building API now reduces future rework. 


Cons:

Infra load on Atlas increases with each sync call; Atlas is already heavily loaded. 

Excluding P360 reduces business’ usual control surface; requires ML-owned segmentation or moving segments to Atlas (both undesirable for this initiative). 

Security / network work for new sync connectivity and SLOs (timeouts, retries, back-pressure). 

Higher engineering effort vs the quick-win async path. 

### Option 2 — Async via Pub/Sub -> P360 

**Pros (from discussion):**

- **Quick-win** path: limited incremental work; ML team effort small (“almost no effort” beyond ingesting more realtime Pub/Sub).
- **Infra-friendly**: avoids additional **sync** pressure on Atlas; aligns with **Pub/Sub worker** strategy.
- Preserves **P360** as the **source for segments** (business familiar tooling & governance).
- With proper worker config, events can be registered **< 1 min**; target **2–3 min** discussed.
- Maintains **session consistency** (no mid-session flip unless we intentionally change that behavior).

**Cons (from discussion):**

- **Atlas refresh at init** means **no mid-session** effect today; needs explicit **“refresh on selected events”** to matter inside a session.
- **Eventual consistency**; cannot satisfy strict sub-second paths (Autobet-like).
- **Coordination with P360** to refresh specific attributes/segments more frequently; dependency risk.
- If ML should own **segmentation thresholds**, P360’s ability for business to tweak segments can conflict with ML governance.
## Open Questions

1. **Atlas refresh policy**: can Atlas refresh selected P360 attributes/segments on **specific in-session events** (beyond init)? Needed for Option 2 to influence a live session

2. **Segmentation ownership**: for ML segments, do we formalize **ML ownership** (API or P360 constraints) to avoid ad-hoc business overrides?

## Proposed two-track approach:
**Now:** Go with **Option 2** for the current initiative. Set up event-based P360 refresh for selected segments to make updates more dynamic inside sessions.
**Next:** Build the foundation for **Option 1** to enable future real-time use cases.

## Pros & Cons (executive matrix)

| Dimension               | Option 1 — Sync API                                      | Option 2 — Async → P360                                                            |
| ----------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| **Latency**             | **Real-time** (sub-second to ~hundreds ms)               | **Near-real-time** (≈2–3 min with tuning)<br>                                      |
| **Atlas Impact**        | Higher (per-event sync calls)                            | Lower (no extra sync calls)<br>                                                    |
| **Session Consistency** | Can reflect mid-session changes immediately (if desired) | Traditionally **no mid-session flips**; can add targeted refresh for selected <br> |
| **Business Control**    | ML-owned segmentation via API; change via ML process     | P360-native control; familiar workflows<br>                                        |
| **Effort**              | Higher (API, networks, SLOs, fallbacks)                  | Lower (quick-win; worker tuning; P360 coordination)                                |
| **Risk**                | Needs robust rate-limiting, retries, circuit breakers    | Eventual consistency; dependency on P360 refresh policy[                           |
| **Future Fit**          | **Required** for Autobet & similar sync models           | Great for bulk segments; not suitable for strict sync gates                        |


|   |   |   |
|---|---|---|
|Latency SLO|Sub-second to ~hundreds of ms possible (infra-dependent).|Minutes-level (2–3 min goal with tuning).[<br><br>prop2p<br><br>](https://drive.google.com/file/d/1IgGlxrWq0wnTn6d2l2ph5MpoT8KLrDAxq0-d73ufrsk)|

|            |                                    |                                  |
| ---------- | ---------------------------------- | -------------------------------- |
| Atlas Load | **Higher** (per-event sync calls). | **Lower** (no extra sync calls). |

|            |                                                     |                                                                                                                                                   |
| ---------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Resilience | Needs timeouts, retries, fallbacks (degraded mode). | Queue absorbs spikes; eventual consistency.[<br><br>prop2p<br><br>](https://drive.google.com/file/d/1IgGlxrWq0wnTn6d2l2ph5MpoT8KLrDAxq0-d73ufrsk) |

|   |   |   |
|---|---|---|
|Governance|ML owns thresholds in API; business control via API config/workflow.|Business keeps P360 workflows; ML governance must be clarified.[<br><br>prop2p<br><br>](https://drive.google.com/file/d/1IgGlxrWq0wnTn6d2l2ph5MpoT8KLrDAxq0-d73ufrsk)|

|   |   |   |
|---|---|---|
|Security/Net|New egress/ingress paths, authN/Z for API.|Existing patterns; least new surface.[<br><br>prop2p<br><br>](https://drive.google.com/file/d/1IgGlxrWq0wnTn6d2l2ph5MpoT8KLrDAxq0-d73ufrsk)|

|                       |                                           |                                                                                                                                                            |
| --------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Implementation Effort | Higher (networks, API, SLOs, monitoring). | Lower (quick-win; worker tuning; P360 coordination).[<br><br>prop2p<br><br>](https://drive.google.com/file/d/1IgGlxrWq0wnTn6d2l2ph5MpoT8KLrDAxq0-d73ufrsk) |
I’ve tried to put together all the pros and cons that were mentioned in the meeting and on Slack into one document.

Meeting summary with GameOps:
**Meeting summary**

## Quick recap

The team discussed their vision to automate campaign configuration and optimization processes using AI, with a focus on reducing manual efforts and improving efficiency through real-time processing capabilities. They explored technical limitations and requirements for implementing AI models, including discussions about LLM execution constraints and the need for separate development and validation environments. The team agreed to develop a framework with templates and security features, allowing different AI assistants to integrate with various data sources and tools, with follow-up communications to be handled through Slack or email.

## Next steps

- [Serhii to share the intake process documentation with the team via email.](https://url.us.m.mimecastprotect.com/s/bezVCwpx60tL91P16fqh4uJa_Lg?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/bezVCwpx60tL91P16fqh4uJa_Lg?domain=tasks.zoom.us")
- [Serhii to establish a communication channel for AI project requests.](https://url.us.m.mimecastprotect.com/s/B60OCxkyW6iJwABA8hYinuyMNyN?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/B60OCxkyW6iJwABA8hYinuyMNyN?domain=tasks.zoom.us")
- [Serhii's team to create an epic in Jira for the near real-time properties feature.](https://url.us.m.mimecastprotect.com/s/C9O3CyPzWwfNkMJMDfRs6uxtgCO?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/C9O3CyPzWwfNkMJMDfRs6uxtgCO?domain=tasks.zoom.us")
- [D1's team to provide business requirements for what LLM should be executed and which properties should be processed in real-time.](https://url.us.m.mimecastprotect.com/s/GfoVCzpAg6tR92n2DUotAu9UcHW?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/GfoVCzpAg6tR92n2DUotAu9UcHW?domain=tasks.zoom.us")
- [D1's team to align internally on AI implementation priorities before further discussions.](https://url.us.m.mimecastprotect.com/s/nUEDCADrE6H92WVW0fMu1uG9O9S?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/nUEDCADrE6H92WVW0fMu1uG9O9S?domain=tasks.zoom.us")
- [Serhii's team to develop framework with templates, validation, testing, and security for AI implementation.](https://url.us.m.mimecastprotect.com/s/HmcOCBBvGrtVB4A4qfvC3u2qRx0?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/HmcOCBBvGrtVB4A4qfvC3u2qRx0?domain=tasks.zoom.us")
- [Serhii's team to explore AI solution for Mika's request regarding monthly calendar planning based on historical campaign data.](https://url.us.m.mimecastprotect.com/s/JiPrCDkxKqiB0VMVpCBFZujnpv7?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/JiPrCDkxKqiB0VMVpCBFZujnpv7?domain=tasks.zoom.us")

## Summary

### AI-Driven Campaign Automation Strategy

Serhii discussed the team's vision to automate campaign configuration and optimization processes using AI, with a focus on reducing manual efforts and improving efficiency. He mentioned ongoing work on near real-time properties, collaborating with Dennis, and the need for specific requirements regarding LLM execution and property analysis. Serhii also highlighted the flexibility of intake processes, including a form similar to the Innovation Lab's, and offered to share a detailed workflow diagram for better understanding.

### AI Transition and Processing Challenges

The team discussed transitioning from traditional machine learning models to AI, with Serhii explaining that while some models currently run on Vertex in the cloud, they are limited by long processing times of 15-20 minutes. They are exploring real-time processing capabilities with the Infrastructure team and plan to determine which properties and messages should be processed in real-time, with AI then analyzing the data and making recommendations based on both historical data and user clusters. C7 raised a technical limitation regarding the 1 million token context limit for LLMs, suggesting the need to carefully manage data volume and potentially use an agent that can process data in smaller chunks to work around this constraint.

### AI Model Validation and Testing

Serhii and D1 discussed the development and validation of AI models, emphasizing the need to keep them separate from ongoing data science AB testing. They proposed using sandbox and UAT environments for testing and validation, while also addressing data security concerns, including API and AI security. D1 inquired about the validation process for the AI models, to which Serhii suggested following the usual process of launching AI models under AB test scrutiny.

### AI Framework Development and Integration

The team discussed implementing AI models, with Serhii explaining they are developing a framework with templates, validation, testing, and security features, allowing each agent to operate in a sandbox environment. Serhii described how different AI assistants could work with specific data sources and tools, including integrations with Slack, Confluence, and calendar systems. The team agreed to follow up with more detailed requests through either a Slack channel or email, as this would require ongoing collaboration and regular conversations.

**Meeting with GameOps summary:**
Meeting assets for AI + GameOps teams: introduction are ready!

**Meeting summary**

## Quick recap

The team discussed their vision to automate campaign configuration and optimization processes using AI, with a focus on reducing manual efforts and improving efficiency through real-time processing capabilities. They explored technical limitations and requirements for implementing AI models, including discussions about LLM execution constraints and the need for separate development and validation environments. The team agreed to develop a framework with templates and security features, allowing different AI assistants to integrate with various data sources and tools, with follow-up communications to be handled through Slack or email.

## Next steps

- [Serhii to share the intake process documentation with the team via email.](https://url.us.m.mimecastprotect.com/s/bezVCwpx60tL91P16fqh4uJa_Lg?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/bezVCwpx60tL91P16fqh4uJa_Lg?domain=tasks.zoom.us")
- [Serhii to establish a communication channel for AI project requests.](https://url.us.m.mimecastprotect.com/s/B60OCxkyW6iJwABA8hYinuyMNyN?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/B60OCxkyW6iJwABA8hYinuyMNyN?domain=tasks.zoom.us")
- [Serhii's team to create an epic in Jira for the near real-time properties feature.](https://url.us.m.mimecastprotect.com/s/C9O3CyPzWwfNkMJMDfRs6uxtgCO?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/C9O3CyPzWwfNkMJMDfRs6uxtgCO?domain=tasks.zoom.us")
- [D1's team to provide business requirements for what LLM should be executed and which properties should be processed in real-time.](https://url.us.m.mimecastprotect.com/s/GfoVCzpAg6tR92n2DUotAu9UcHW?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/GfoVCzpAg6tR92n2DUotAu9UcHW?domain=tasks.zoom.us")
- [D1's team to align internally on AI implementation priorities before further discussions.](https://url.us.m.mimecastprotect.com/s/nUEDCADrE6H92WVW0fMu1uG9O9S?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/nUEDCADrE6H92WVW0fMu1uG9O9S?domain=tasks.zoom.us")
- [Serhii's team to develop framework with templates, validation, testing, and security for AI implementation.](https://url.us.m.mimecastprotect.com/s/HmcOCBBvGrtVB4A4qfvC3u2qRx0?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/HmcOCBBvGrtVB4A4qfvC3u2qRx0?domain=tasks.zoom.us")
- [Serhii's team to explore AI solution for Mika's request regarding monthly calendar planning based on historical campaign data.](https://url.us.m.mimecastprotect.com/s/JiPrCDkxKqiB0VMVpCBFZujnpv7?domain=tasks.zoom.us "https://url.us.m.mimecastprotect.com/s/JiPrCDkxKqiB0VMVpCBFZujnpv7?domain=tasks.zoom.us")

## Summary

### AI-Driven Campaign Automation Strategy

Serhii discussed the team's vision to automate campaign configuration and optimization processes using AI, with a focus on reducing manual efforts and improving efficiency. He mentioned ongoing work on near real-time properties, collaborating with Dennis, and the need for specific requirements regarding LLM execution and property analysis. Serhii also highlighted the flexibility of intake processes, including a form similar to the Innovation Lab's, and offered to share a detailed workflow diagram for better understanding.

### AI Transition and Processing Challenges

The team discussed transitioning from traditional machine learning models to AI, with Serhii explaining that while some models currently run on Vertex in the cloud, they are limited by long processing times of 15-20 minutes. They are exploring real-time processing capabilities with the Infrastructure team and plan to determine which properties and messages should be processed in real-time, with AI then analyzing the data and making recommendations based on both historical data and user clusters. C7 raised a technical limitation regarding the 1 million token context limit for LLMs, suggesting the need to carefully manage data volume and potentially use an agent that can process data in smaller chunks to work around this constraint.

### AI Model Validation and Testing

Serhii and D1 discussed the development and validation of AI models, emphasizing the need to keep them separate from ongoing data science AB testing. They proposed using sandbox and UAT environments for testing and validation, while also addressing data security concerns, including API and AI security. D1 inquired about the validation process for the AI models, to which Serhii suggested following the usual process of launching AI models under AB test scrutiny.

### AI Framework Development and Integration

The team discussed implementing AI models, with Serhii explaining they are developing a framework with templates, validation, testing, and security features, allowing each agent to operate in a sandbox environment. Serhii described how different AI assistants could work with specific data sources and tools, including integrations with Slack, Confluence, and calendar systems. The team agreed to follow up with more detailed requests through either a Slack channel or email, as this would require ongoing collaboration and regular conversations.

Olenka meeting: 
Configs bulk action - activate/deactivate
Configs bulk action - add rules
Legal check in Sprout and in Jira ticket.