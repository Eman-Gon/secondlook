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

## What to say — about three minutes with the demo

*[Start on the Secondlook dashboard.]*

“You upgrade a library your app depends on. Your tests pass. Everything looks fine. Then a customer does something your tests never covered—and it breaks.

“Developers already reduce this risk by locking versions, reviewing updates through tools like Dependabot, and running automated tests. Renovate also estimates upgrade risk using other projects’ results.

“But what happens to the behavior your tests missed?

“I built Secondlook to explore that gap. Its dashboard flags code matching known compatibility risks. For prepared cases, it brings together documentation, before-and-after tests, and a tested fix.

*[Open the customer-import case. Point to the existing tests passing on both versions.]*

“This app requires a customer’s name, but customers should be able to leave out their nickname. After upgrading Pydantic—the library that checks the input—leaving out the nickname causes an error. The existing tests still pass because they never try that situation.

*[Point to the targeted check: Pass / Fail.]*

“These are saved results from a successful live run. The same targeted test passes on the old version and fails on the new one, in isolated Docker environments.

*[Show the patch and the fixed check: Pass / Pass.]*

“We then test a prepared one-line fix—adding an explicit default—and the same check passes on both versions.

*[Switch to Bright Data → MCP → Event Log, as in the second screenshot. Point to the 19:42:09 row.]*

“Bright Data supplies the documentation explaining the change. This is Bright Data’s own log showing a successful request to Pydantic’s website using scrape_as_markdown. The client name, commit-watch, is my app’s internal name. A guided Strands agent using Groq uses the retrieved documentation to propose test data for the selected scenario.

*[Switch to the Cognee graph in the first screenshot: secondlook_upgrade_evidence. Point to customer.nickname, the Pydantic version, probe_old/probe_new, and fix_patch.]*

“Here’s a graph I created by uploading the investigation evidence to Cognee Cloud. You can see the affected nickname field, the dependency version, the before-and-after checks, and the fix organized as connected information.

*[Return to Secondlook’s Cognee evidence cards: Stored and retrieved, Inspect retrieved evidence, and previous findings: Recalled.]*

“The application also stores and retrieves findings automatically in separate Cognee datasets. This successful run recalled an earlier finding for the same app and versions, reran the tests, and stored the verified result.

“Today, the prototype checks known risks and verifies prepared cases. The broader goal is to help developers answer: what could this upgrade break in my app, and how can I check?

“Bright Data retrieves the source. Docker verifies the behavior. Cognee remembers the result. That’s Secondlook.”

## Screenshot and demo notes

- The first screenshot selects `secondlook_upgrade_evidence`: the manually uploaded evidence document, with 22 extracted entities. Its visible nodes include `customer.nickname`, `pydantic 1.10.18`, `existing_old`, `existing_new`, `probe_old`, `probe_new`, `fixed_old`, `fixed_new`, and `fix_patch`.
- `existing` means the original tests; `probe` means the added missing-nickname check; `fixed` means that check after the fix. `old` and `new` identify the dependency version being tested.
- This manual graph is separate from the automatic run’s dataset, `secondlook_compatibility_098a753197e04755befef498f95f612a`. Use the dashboard’s storage/retrieval receipts to demonstrate that automatic workflow. A graph visualization alone does not establish that tests ran or evidence was recalled.
- The second screenshot shows [Bright Data’s MCP Event Log](https://brightdata.com/cp/mcp/event_log). Show the September 21, 19:42:09 row: `scrape_as_markdown`, `commit-watch`, `pydantic.dev`, `success`. This matches the saved successful Cloud rehearsal.
- Presentation order: dashboard test results → tested fix → Bright Data event log → manual Cognee graph → dashboard automatic memory receipts → closing line.

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
