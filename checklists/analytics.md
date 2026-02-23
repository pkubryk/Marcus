# Analytics Instrumentation Checklist

Review the feature for analytics instrumentation needs.

## Event Identification
- User interactions that indicate engagement or friction identified
- Funnel steps and conversion points mapped
- Success and failure states tracked
- Error events captured with context (not PII)

## Privacy Compliance
- All tracking uses anonymous/hashed identifiers
- No PII in event properties
- Behavioral events only — actions, not content
- User opt-out preferences respected
- Data minimization — only collect what drives decisions

## Implementation
- Events follow a consistent naming convention
- Event properties are typed and documented
- Funnel events are ordered and traceable
- Performance impact of tracking is negligible
- Events fire reliably (not lost on navigation/crash)

## Metrics Definition
- Success metrics defined for this feature
- Baseline measurements identified
- Alert thresholds set for anomalies
- Dashboard or query for reviewing metrics exists
