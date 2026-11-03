# Arc 0.51.89 — Native app discovery and desktop recovery

This update repairs failures reproduced while asking Arc to open and use
Windows Calculator. Actual installed-app acceptance is recorded separately in
QA_0.51.89.md; passing source checks does not establish task completion.

Desktop requests now discover installed supported applications before the
model chooses what to open. Exact returned app identities remain available
through context fitting. Loading tool descriptions is no longer mistaken for
discovering installed apps.

When a launch is rejected before dispatch, Arc can refresh the app list and
return the current identities. Expired desktop observations can trigger a
bounded read of the same previously observed window. These recovery reads do
not repeat a launch, click or keystroke. Unknown outcomes still require fresh
inspection.

Invalid PC arguments return bounded field-level corrections alongside the
exact action schema. Diagnostics record the supported action name and PC error
code without retaining arguments or private window contents.

Successful desktop inputs receive an immediate inspection of the same window.
The next model step can use that fresh view directly instead of spending a
separate generation round asking to look. Expired element references require
a fresh comparison of the complete accessible window state; changed or
incomplete views are returned for reconsideration instead of replaying input.

The desktop conversation keeps stable instructions at the beginning of model
requests and places changing task state beside the latest result. This lets
the local runtime reuse more cached prompt processing between actions,
especially on CPU and smaller models. Current observations, pending outcomes,
the original request and the existing permission checks remain in force.

App launch and delivered input are intermediate steps. Arc must inspect the
resulting app state and compare it with the user's request before reporting
completion. This update does not change model weights or enable PC access
without the user's session connection.
