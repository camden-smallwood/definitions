# Index targets

`index_target` is editor metadata on a block-index or block-flags field. It
describes the target without changing the field's binary type or layout.
`blam-tags::index_targets::IndexTargets` resolves it; editors use the same
loaded table for dropdown choices and index repair after structural edits.

```json
{ "type": "custom_short_block_index", "name": "initial task",
  "index_target": { "selector": "initial objective", "child": "tasks" } }
{ "type": "custom_short_block_index", "name": "point",
  "index_target": { "selector": "../point set", "child": "points" } }
{ "type": "custom_short_block_index", "name": "damage section",
  "index_target": { "path": "new damage info[0]/damage sections" } }
```

- `path` is root-relative. Add `"base": "holder"` to start in the struct
  containing the referencing field instead; leading `../` steps then climb
  enclosing entries, as in `{ "path": "../Actors", "base": "holder" }`.
- `selector` is relative to that holder. Each leading `../` climbs one
  enclosing struct or block entry. Resolve the selector's own index target,
  select its current entry, then find the `child` block in that entry.
- An unset/out-of-range selector or a missing selected entry has no target.
  A different parent's block must never be substituted.
- Unannotated ordinary indices and flags retain their declared target search
  in the holder and its ancestors.

Rules match GUID or struct name plus clean field-name occurrence, excluding
explanation headings, so they also apply to saved layouts. Invalid or
conflicting rules are load errors: structural edits must not proceed with a
silently empty replacement table.

These annotations migrate Baboon's existing custom-index lookup behavior.
They do not define whether a dependent child index should reset when its
parent reference is cleared; that behavior still needs native-editor evidence.
