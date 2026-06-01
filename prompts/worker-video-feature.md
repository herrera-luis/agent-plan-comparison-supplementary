<!--
Frozen prompt body for the worker-video-feature benchmark.

The prompt is sent verbatim to every (tool, leg) cell. The SHA-256 fingerprint
at the bottom is computed over the byte range strictly between the markers
below (exclusive on both sides):

    awk '/^--- prompt body begins ---$/{flag=1;next} /^--- prompt body ends ---$/{flag=0} flag' \
      research/agent-plan-comparison-final/prompts/worker-video-feature.md \
      | shasum -a 256
-->

--- prompt body begins ---
"/opsx-propose" I need you to review in detail the jira ticket https://cognitiverun.atlassian.net/browse/CR-VIDEO-001 
also review the comment of that ticket that constains some instructions.
In the ticket there is a code reference to github so let's use the required mcp to review it.
Also let's implement test to validate that the new feature works as expected.

IMPORTANT: When using the GitHub MCP to read code from `Cognitiverun/core-services`, you MUST pass `ref: feature/bench-30b69674` (not the default branch). The video feature has already been implemented and merged to `main`; reading from `main` would contaminate your plan with the existing implementation. The branch `feature/bench-30b69674` is the frozen pre-feature reference state that all planning runs must use. For any other repository (e.g. `Cognitiverun/api`), reading from `main` (or the SHA cited in the Jira ticket) is fine.
--- prompt body ends ---

<!-- sha256: a9db692e2170a93d0118e0b40123c7b5ec3cd8d69744b5e782e02ca09ecaa801 -->
