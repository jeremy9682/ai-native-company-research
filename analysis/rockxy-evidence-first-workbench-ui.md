# Evidence-First Operational Workbench Patterns from Rockxy

## Summary

[Rockxy](https://github.com/RockxyApp/Rockxy) is a native macOS network debugging application. Its SwiftUI/AppKit shell is not a drop-in template for a browser-based ERP, but its interaction contracts are highly reusable for dense operational workbenches:

- keep the selected record and its context stable;
- place details, evidence, and assistance next to the selected object;
- separate observed facts, derived results, inferences, and unknowns;
- preview the exact outbound dataset before an AI request is sent;
- make redaction, truncation, provider, model, and destination visible;
- treat saved focus sets and temporary noise exclusion as different concepts;
- validate localization coverage and placeholder integrity mechanically.

The most important adaptation principle is: **borrow the trust and context model, not the native three-pane shell.**

## Source Snapshot

- Repository: [RockxyApp/Rockxy](https://github.com/RockxyApp/Rockxy)
- Reviewed commit: [`4eff247e71b293131ebd87a31ead88914cbc7484`](https://github.com/RockxyApp/Rockxy/tree/4eff247e71b293131ebd87a31ead88914cbc7484)
- Accessed: 2026-08-30
- Complementary platform references: [Frappe translation basics](https://docs.frappe.io/framework/user/en/guides/basics/translations) and [Frappe translation API](https://docs.frappe.io/framework/user/en/translations)

All implementation observations below refer to the pinned Rockxy commit. Product applications are proposals, not claims made by Rockxy or Frappe.

## What the Interface Actually Does

### A native, context-oriented split layout

The current root view defines a native three-part workspace: navigation on the left, a flexible central list, and a context dock on the right. The default window is approximately 1200×760 with a 960×620 minimum; the sidebar and context dock have bounded ideal widths rather than consuming arbitrary space. See [`ContentView.swift`](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/Rockxy/ContentView.swift).

The workspace split assigns semantic roles to the sidebar and inspector and allows them to collapse. See [`NativeWorkspaceSplitView.swift`](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/Rockxy/Views/Main/NativeWorkspaceSplitView.swift). This matters more than the exact pane widths: collapsing presentation does not have to destroy the user's selection or investigative context.

The right-hand context area combines `Details` and `AI Assistant` under the same selected-object context. See [`ContextDockView.swift`](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/Rockxy/Views/Inspector/ContextDockView.swift). The assistant is therefore structurally subordinate to the evidence being inspected rather than presented as a detached chatbot.

### Focus and noise are different operations

Rockxy's [filtering and search documentation](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/docs/core-features/filters-and-search.mdx) distinguishes reusable Focus Sets from temporary Noise Control. Noise exclusion changes what the user sees without deleting captured records, and the current selection remains meaningful.

For an operational system, this suggests two separate features:

1. **Saved worksets** preserve a named task context: filters, scope, and sort order.
2. **Temporary exclusions** hide acknowledged noise for the current session, show an exclusion count, and provide an obvious reset.

Combining these concepts into one opaque filter state makes it hard to tell whether a record is outside the business scope, temporarily hidden, or genuinely absent.

## The Strongest Reusable Pattern: Evidence Before AI

Rockxy's [AI Assistant documentation](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/docs/features/ai-assistant.mdx) describes a built-in local deterministic analysis path and an explicit review step for model-bound data. Its source UI exposes the exact reviewed content, redaction summary, truncation and omission notes, and model destination before an outbound request. See [`DebugAssistantReviewDataSheet.swift`](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/Rockxy/Views/Inspector/DebugAssistantReviewDataSheet.swift).

This supports a durable operational pattern:

```text
Selected record
    ↓
Authorized projection
    ↓
Evidence classification
    ↓
Exact outbound-data preview
    ↓ explicit confirmation
Read-only AI interpretation
    ↓ separate governed action
System-of-record effect
```

The steps should not collapse into a single “Ask AI” button. Each boundary answers a different question:

- **Selected record:** What is the user acting on?
- **Authorized projection:** What is this user allowed to read in this scope?
- **Evidence classification:** Which values are observed, computed, inferred, or unknown?
- **Outbound preview:** What exactly will leave the application?
- **Read-only interpretation:** What did the model conclude, from which model and evidence?
- **Governed action:** Which authoritative workflow, permission check, confirmation, and audit trail will create a business effect?

### A practical evidence vocabulary

Rockxy uses a vocabulary equivalent to observed, derived, inferred, and unknown. An operational workbench can apply the same separation:

| Evidence class | Meaning | Example presentation rule |
|---|---|---|
| Observed | Directly stored or received fact | Show source record and timestamp. |
| Derived | Deterministic calculation | Show the rule or formula version. |
| Inferred | Model or heuristic conclusion | Show model/method, confidence boundary, and supporting evidence. |
| Unknown | Required fact not established | Keep visible; never silently coerce to zero, false, or a normal status. |

Source navigation should be honest. If a source record can be opened, provide the link. If it cannot, render a disabled or text-only reference rather than a fake control.

## Layout Adaptation for Browser-Based Workbenches

Rockxy is a desktop-native application with a minimum width near 960 pixels. Its three-pane shell is not evidence that a web workbench should keep three permanent columns at every breakpoint.

A safer adaptation ladder is:

1. Reuse an existing record drawer or inspector.
2. Add `Details` and `Evidence` tabs.
3. Add a read-only `Assistant` tab only after the evidence contract exists.
4. Promote the same component to an optional wide-screen dock only if measured task performance justifies it.
5. Consider a bottom inspector only for a real two-surface task such as document comparison or audit diffs.

This sequence protects the main table and avoids creating competing detail surfaces.

### Suggested responsive contract

| Width class | Recommended treatment |
|---|---|
| Wide desktop | Dense table plus optional collapsible record inspector. |
| Standard laptop | Table plus overlay drawer; do not reserve a permanent third column. |
| Tablet / narrow desktop | Collapsible navigation and full-height record drawer. |
| Mobile | One primary surface at a time; the inspector becomes full-screen. |

At every width, verify document-level horizontal overflow, control reachability, readable labels, and preservation of selection and filter state. A desktop screenshot alone cannot validate the responsive contract.

## Localization Governance

Rockxy's [localization guide](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/docs/development/localization.mdx) uses native string catalogs, a system-default language option, runtime switching for explicit choices, and a bundle-derived language list.

At the pinned commit, a source audit found:

- 3,858 entries in `Localizable.xcstrings`;
- 116 entries marked as not requiring translation;
- 3,742 translatable entries;
- complete Simplified Chinese entries for those 3,742 strings;
- a passing non-compiling run of the repository's [`validate_xcstrings.py`](https://github.com/RockxyApp/Rockxy/blob/4eff247e71b293131ebd87a31ead88914cbc7484/.github/tools/validate_xcstrings.py).

The exact Apple file format is platform-specific. The reusable governance is not:

- one runtime source of truth for the active language;
- a capability-derived language list rather than a second hard-coded list;
- extractable source strings;
- non-empty required translations;
- placeholder parity across languages;
- regression coverage for runtime locale, cached strings, toolbar labels, and settings;
- a clear boundary between stable internal identifiers and translated user-facing labels.

### Applying the pattern to Frappe

Frappe provides its own native path. User-visible Python strings use `_()`, JavaScript strings use `__()`, and app translations live in `translations/[lang].csv`. Literal templates are important because dynamic values cannot be extracted reliably. The active language should come from the Frappe user preference rather than a parallel application-specific switch.

A minimal rollout is:

1. choose one canonical source language;
2. define a controlled business glossary;
3. translate the shared shell, states, and common components first;
4. add one complete target-language CSV before advertising broader language support;
5. validate placeholder parity and non-empty coverage in CI;
6. test long labels at narrow widths;
7. separate language from locale and currency semantics.

For example, a currency code is business data. Its symbol, grouping, compact notation, and date formatting are locale presentation. Translating the label must not change the ledger meaning.

## What to Borrow, Adapt, and Reject

| Rockxy pattern | Decision | Operational-workbench application |
|---|---|---|
| Stable selection and context | Borrow | Preserve selected row, scroll position, and inspector state across collapse/filter operations. |
| Compact command layer | Borrow | Keep high-frequency controls visible and move secondary filters into a reversible disclosure. |
| Three permanent panes | Reject as default | Start with the existing shell and a drawer; promote to a dock only after measurement. |
| Focus Sets | Adapt | Saved, named business worksets scoped to the current authorization context. |
| Noise Control | Adapt | Temporary exclusion with a count and clear action; never data deletion. |
| Evidence classes | Strongly borrow | Separate observed, derived, inferred, and unknown data. |
| Source reveal | Strongly borrow | Navigate to the authoritative record when possible. |
| Exact outbound-data review | Strongly borrow | Make it mandatory before external model calls. |
| Read-only assistant | Strongly borrow | Keep suggestions separate from authoritative effects. |
| Apple string catalogs | Reject as a cross-platform format | Use the host framework's localization mechanism. |
| Localization validator | Strongly borrow | Check coverage, empties, placeholders, and runtime regressions. |
| Very small native table typography | Reject for multilingual web UI | Preserve readable CJK and long translated labels. |

## Implementation Checklist

### Context and evidence

- [ ] The selected record is explicit and stable.
- [ ] Every evidence item is classified.
- [ ] Source references are real or visibly unavailable.
- [ ] Unknown values remain unknown.
- [ ] The evidence payload uses the same authorization scope as the primary read path.

### AI boundary

- [ ] The exact outbound content is previewable.
- [ ] Provider, model, and destination are visible.
- [ ] Redactions, truncations, omissions, and extra scope are listed.
- [ ] Sending requires an explicit confirmation.
- [ ] AI output is read-only until a separate governed action is confirmed.

### Layout

- [ ] The main table remains the primary work surface.
- [ ] Only one detail surface is persistent at a time.
- [ ] Selection and filters survive panel collapse.
- [ ] Standard laptop and mobile widths have explicit contracts.
- [ ] No document-level horizontal overflow makes primary controls unreachable.

### Localization

- [ ] The host framework owns the active language.
- [ ] User-visible strings are extractable.
- [ ] Internal codes remain stable.
- [ ] Required translations are complete and non-empty.
- [ ] Placeholder parity is tested.
- [ ] Long translated labels and locale-specific formatting are tested independently.

## Conclusion

Rockxy demonstrates that an AI-assisted workbench becomes trustworthy when assistance is attached to a stable selected object, evidence is classified, outbound data is reviewable, and authoritative effects remain separate. Those contracts transfer well across domains. Its native three-pane geometry and Apple-specific localization format do not.

For most operational web systems, the smallest useful adaptation is an evidence-aware record drawer plus a framework-native localization gate. A permanent AI dock should be earned by measured workflow benefit, not copied because it looks sophisticated.
