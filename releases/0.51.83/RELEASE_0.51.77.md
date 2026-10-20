# Arc 0.51.77 — shared workstation task flow

Arc now has a common task lifecycle around its existing conversation, research, coding, browser, vision and project paths. The model receives compact current-task receipts so it can continue from saved work and check results without repeatedly rebuilding the whole task history.

- Added a bounded receipt record with file identities and hashes, source reads, saved screenshots and successful vision analyses.
- Kept syntax and behavior-test results separate. Editing a file invalidates passes for the earlier revision in the task record.
- Added an expandable “Recorded work” summary and activity phases to the existing thinking panel, using the same theme.
- Added eight detailed workstation workflows in Settings → Plugins. Search for “Workstation”.
- Preserved existing tools, features, saved-chat compatibility, permission checks and completion/failure guards. No additional planner calls or model reloads are introduced by this layer.

The compact record uses the selected model's context; it does not provide unlimited context or guarantee task completion. Source reads, screenshots, vision and code tests each retain their own evidence requirements. See `WORKSTATION_ARCHITECTURE.md` for implementation and limits.
