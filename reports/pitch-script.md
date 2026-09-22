# Secondlook: pitch and guided demo

Updated September 21, 2026. Readiness: working prototype with a verified, prepared Pydantic investigation using Bright Data and Cognee Cloud. Evidence is the saved successful Cloud rehearsal `20260922T024137Z-296a3fdf`; this pitch revision did not rerun services. Existing-tool context was checked against official documentation. Allow about three minutes with the dashboard and both sponsor screens.

## Three-result demo: two changes needed, then a passing result

Use two measured upgrade failures and one corrected passing case. There are currently only two prepared comparison cases in the dashboard; the third result below is the corrected Pydantic case, not a third independent repository or an installation-safety verdict.

| Order | Select / point to | Say |
| --- | --- | --- |
| 1 | GPU energy recommender, original-code comparison | “These two data-collection functions work with pandas 2.3.3 and fail with pandas 3.0.0. The hourly frequency changes from uppercase H to lowercase h.” |
| 2 | Customer import demo, missing-nickname check | “This input works with Pydantic 1.10.18 and fails with 2.8.2. Preserving the old behavior requires an explicit default.” |
| 3 | Customer import demo, With suggested fix: Pass / Pass | “Here is the corrected version. The targeted check passes on both dependency versions.” |

Call the third result “Passed the compatibility check shown.” Do not say “everything is safe” or “installation has no errors”: these results measure selected runtime behavior in prepared environments, not a complete installation or application assessment. Public scans with zero findings are not substitutes for a measured passing result.

Three-minute timing: problem and introduction 0:00–0:30; pandas failure 0:30–0:55; Pydantic failure 0:55–1:20; corrected passing result 1:20–1:35; Bright Data log 1:35–2:00; Cognee graph 2:00–2:25; automatic recall cards 2:25–2:45; closing 2:45–3:00. Bright Data and Cognee integration evidence belongs to the Pydantic investigation; the pandas case is a prepared Docker comparison.

## Full three-minute pitch with screen links

Open these tabs before presenting:

- [Secondlook dashboard](http://127.0.0.1:8765/) — requires the local server.
- [Secondlook source repository](https://github.com/Eman-Gon/secondlook) — matches the configured Git remote.
- [GPU energy recommender repository](https://github.com/Eman-Gon/gpu-energy-recommender).
- [Bright Data MCP Event Log](https://brightdata.com/cp/mcp/event_log) — signed-in account.
- [Cognee Cloud knowledge graph](https://platform.cognee.ai/knowledge-graph) — signed-in account; select `secondlook_upgrade_evidence` to match the supplied screenshot.

### 0:00–0:30 — Problem and introduction

*[Show the Secondlook dashboard. Keep the source repository available for questions.]*

“You upgrade a library. Your tests pass. Then a customer does something your tests never covered—and it breaks.

“Teams already lock dependency versions, use tools like Dependabot, and run automated tests. Renovate also estimates risk from other projects’ results.

“I built Secondlook to explore what those tests might miss: flagging known compatibility risks and, for prepared cases, bringing together documentation, before-and-after checks, and tested fixes.”

### 0:30–0:55 — First change needed: pandas

*[In the dashboard, select GPU energy recommender. Its source is in the repository linked above; the measured snapshot is https://github.com/Eman-Gon/gpu-energy-recommender/blob/7a802eace3a3775acc5ea4af6c266079e2599eb3/eda/data_collection.py .]*

“First, two data-collection functions from my GPU energy repository. They pass with pandas 2.3.3 but fail with pandas 3.0.0 because the uppercase hourly-frequency alias was removed. Changing uppercase H to lowercase h makes these checks pass on both versions.”

### 0:55–1:20 — Second change needed: Pydantic

*[Select Customer import demo. Point to existing tests: Pass / Pass, then targeted check: Pass / Fail.]*

“Second, this prepared customer-import example. Customers should be able to leave out their nickname. After upgrading Pydantic, that input is rejected. The existing tests still pass because they never try leaving the nickname out. The targeted check reproduces the break in Docker.”

### 1:20–1:35 — Passing result after the correction

*[Show `nickname: Optional[str] = None` and With suggested fix: Pass / Pass.]*

“Here’s the corrected version. We test a prepared fix: adding an explicit default. The same check now passes on both versions. These are saved results from successful test runs.”

### 1:35–2:00 — Bright Data provider evidence

*[Open the Bright Data Event Log. Point to 2026-09-21 19:42:09: scrape_as_markdown / commit-watch / pydantic.dev / success.]*

“For the Pydantic investigation, Bright Data fetches the official migration guide explaining the change. Here’s its own successful request log. Commit-watch is my app’s internal client name. A guided Strands agent using Groq uses that documentation to propose test data for the selected scenario.”

### 2:00–2:25 — Cognee graph

*[Open Cognee Cloud, select secondlook_upgrade_evidence, and point to customer.nickname, pydantic 1.10.18, probe_old/probe_new, and fix_patch. This is the manually uploaded evidence graph shown in the user's screenshot.]*

“Cognee gives the investigation memory. I uploaded the investigation evidence to create this graph. You can see the affected field, dependency version, before-and-after checks, and fix organized as connected information.”

### 2:25–2:45 — Automatic memory evidence

*[Return to the customer-import dashboard. Show Stored and retrieved, Inspect retrieved evidence, and previous findings: Recalled.]*

“The application also saves and retrieves findings automatically in separate Cognee datasets. This run recalled an earlier finding for the same app and versions, reran the tests, and stored the verified result.”

### 2:45–3:00 — Close

*[Leave the dashboard visible.]*

“Today, this prototype checks known risks and verifies prepared cases. The goal is to help developers investigate what an upgrade could break.

“Bright Data retrieves the source. Docker verifies the behavior. Cognee remembers the result. That’s Secondlook.”

The third result is the corrected second case, not a third independently verified repository. The screenshot's manual graph is separate from automatic dataset `secondlook_compatibility_098a753197e04755befef498f95f612a`. Its 22 extracted entities should not be conflated with the automatic dataset's saved API counts of 22 total nodes and 32 edges. Keep the saved run visible; do not start a fresh live investigation during the timed pitch.

## What to show, in order

1. Open http://127.0.0.1:8765/ and select the customer-import case in **Saved scans & prepared comparisons**. Use this case for sponsor proof.
2. Point at the matrix: **Existing tests: Pass / Pass**, **Targeted check: Pass / Fail**, **With suggested fix: Pass / Pass**. Explain the missing-nickname input in ordinary language.
3. Show the one-line patch: `nickname: Optional[str] = None`.
4. Scroll to **Source and memory evidence**. Under **Bright Data · upstream source**, show **Source retrieved**, the excerpt, and **View upstream source**. The content hash is a receipt for the fetched content, not independent proof that the source is correct.
5. Under **Cognee Cloud · this finding**, show **Stored and retrieved**, then expand **Inspect retrieved evidence**. It contains the actual retrieved failure, fix, versions, and source. Explain that the application validates the returned evidence hash. The saved Cloud rehearsal also verified 22 graph nodes and 32 edges; use **Open Cognee Cloud graph** if time permits and select the matching dataset.
6. Show **Cognee Cloud · previous findings → Recalled**. That is the second-run memory proof: earlier evidence was retrieved and supplied to the model.
7. Keep **Export report** available for questions. If there is extra time, show the pandas case as a separate measured result from two functions in a real repository.

Use the saved rehearsal for the timed pitch. **Run live investigation** repeats the real service path and may take longer than the last successful run. **Run comparison** runs an offline comparison and can replace the visible sponsor cards with a newer offline report; do not use that button to demonstrate fresh sponsor calls. Failed service calls remain failures even when Docker confirms the code behavior.

## What each component proves

| Component | Role | Evidence to show |
| --- | --- | --- |
| Bright Data | Fetch the known official migration page through hosted MCP | Provider, verified attempt, URL, timestamp, excerpt, content hash |
| Strands / Groq | Propose constrained input and expected output for the guided scenario | Saved probe plan; a trusted renderer creates the executable test |
| Docker | Measure behavior before and after the upgrade and test the prepared fix | Same targeted test, actual dependency versions, captured output, isolated runs |
| Cognee | Store/process verified findings, retrieve them, and provide prior evidence to a later run | `stored_and_retrieved`, dataset, returned evidence, hash match, prior recall |

## Answers for judges

**“How do teams handle this already?”** They use lockfiles, review dependency-update pull requests, run automated tests, consult migration guides, and limit exposure through gradual releases. Renovate Merge Confidence and some Dependabot security-update compatibility scores also use results from other projects. Secondlook explores targeted behavior checks and reusable evidence within that existing workflow; no claim of a unique or comprehensive solution is established.

**“Does an existing tool fix your Pydantic example?”** Yes. Pydantic's `bump-pydantic` tool includes BP001, which adds `= None` to optional fields. Its repository is now archived. The demo's value is illustrating and recording the behavior difference and tested fix, not discovering a previously unknown migration.

**“Is this an actual Bright Data call?”** Yes. The latest run records `provider: brightdata`, a verified fetch, and no direct-source fallback. It uses the hosted MCP `scrape_as_markdown` tool on a known migration URL. This demo proves retrieval of that source, not broad autonomous web discovery.

**“How do you know Cognee worked?”** The path is `add → cognify → search`. The application checks the evidence returned by an actual Cognee CHUNKS search against the saved finding’s hash. A subsequent run retrieved an earlier finding and put it into the agent’s context. The local JSON index only locates datasets; it does not substitute for the search response.

**“Where are the graph nodes?”** The successful Cloud rehearsal stored its finding in `secondlook_compatibility_098a753197e04755befef498f95f612a` and verified 22 nodes and 32 edges from the actual dataset graph. Select that dataset in Cognee Cloud. The demonstrated evidence retrieval uses CHUNKS search; it does not claim graph traversal. Earlier local runs and the separate manual Cloud seed are different receipts.

**“Did the agent discover and fix this on its own?”** This is a guided, prepared example. The prompt specifies the missing-nickname scenario, the model proposes constrained test data, and the one-line fix is prepared. The measured before/after behavior is real.

**“Can I run this on any repository?”** The public URL feature checks supported source patterns without executing repository code. Those findings are static and unverified. Measured comparisons currently use prepared cases. The prototype does not establish that arbitrary upgrades are safe.

**“Does memory replace testing?”** No. Prior evidence informs the next investigation, and Docker runs the comparison again. This is retrieved experience, not model training.

## Evidence receipts

- [Readable successful Cloud live report](../.commit-watch/upgrade-demo/20260922T024137Z-296a3fdf/report.txt)
- [Bright Data source receipt](../.commit-watch/upgrade-demo/20260922T024137Z-296a3fdf/source.json)
- [Full report with Cognee Cloud retrieval, graph counts, and prior recall](../.commit-watch/upgrade-demo/20260922T024137Z-296a3fdf/report.json)
- [Separate Cognee Cloud recall check](../.commit-watch/cognee-cloud-prior-recall.json)

## Research behind the existing-tool comparison

- [uv locking and syncing](https://docs.astral.sh/uv/concepts/projects/sync/)
- [Dependabot pull requests and automated test guidance](https://docs.github.com/en/code-security/concepts/supply-chain-security/dependabot-pull-requests)
- [Renovate Merge Confidence](https://docs.renovatebot.com/merge-confidence/)
- [Dependabot compatibility scores](https://docs.github.com/en/code-security/concepts/supply-chain-security/dependabot-security-updates#about-compatibility-scores)
- [Google SRE: canarying releases](https://sre.google/workbook/canarying-releases/)
- [Bump Pydantic's existing optional-field migration rule](https://github.com/pydantic/bump-pydantic#bp001-add-default-none-to-optionalt-uniont-none-and-any-fields)

Use the bounded claim: **“Bright Data retrieves the source, Docker verifies the behavior, and Cognee remembers the verified result.”**
