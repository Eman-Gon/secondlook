# Secondlook

Secondlook inspects public GitHub repositories for known dependency-upgrade patterns and runs prepared before/after compatibility comparisons. The local dashboard includes two measured cases: a reproduced pandas 3 incompatibility in `Eman-Gon/gpu-energy-recommender`, and a Pydantic migration example in a small customer-import fixture.

Each finding is scoped to the source, version pair, and tests shown. These comparisons do not establish that an upgrade is generally safe. The original commit-review commands remain available below.

## Local dashboard

From this workspace, activate the installed environment and start the server:

```sh
source .venv/bin/activate
python -m src.dashboard
```

Open [Secondlook at localhost:8765](http://127.0.0.1:8765). Choose **My repositories** to select one of your account's public repositories, or **Public repository** to enter any public GitHub URL or `owner/repo`, then click **Check repository**. The account defaults to the owner in `TARGET_REPO`; **Change account** loads another GitHub username's public repositories. The picker lists up to 300 repositories and reports unavailable or incomplete listings. The check reads the default branch at a pinned commit, inventories supported Python/JavaScript dependency manifests, and looks for supported upgrade patterns. Findings show their source location, explanation, and upstream reference. Public checks require network access but no GitHub token; private repositories and GitHub rate limits are reported explicitly.

Public checks inspect source without executing repository code or installing its dependencies. Their suggestions are **static and unverified**, not measured failures or tested fixes. “No supported patterns found” does not mean an upgrade is safe. Download/file limits and unsupported coverage appear in the scope and warnings. Recent public checks are retained for the current server session.

The sidebar's [Demo ready collection](http://127.0.0.1:8765/#demo-ready) contains five curated repositories: GPU energy recommender, scam_killer, Gauntlet, Agent-with-a-Brain, and Flask. Each opens saved evidence without a new GitHub request. The first opens the measured pandas comparison when its evidence is confirmed; the remaining examples show a static web-vitals finding or dependency inventories with their coverage limits. The saved source snapshots live in `demo/ready/`, retain their original commit and check date, and survive server restarts independently of recent scan history. Missing or invalid evidence is shown as unavailable. “Demo ready” means ready to present the saved evidence; it does not certify upgrade compatibility.

Each public result includes a plain-English explanation based on its detected dependencies, findings, file count, and coverage warnings, plus a suggested next test. Explanations are generated from the scan evidence without an additional model call and are included in exported scan reports. They distinguish an unsupported dependency from a supported pattern that was checked without finding a match.

The **Saved scans & prepared comparisons** menu opens a previous source scan or a prepared comparison with measured test results and a proposed patch. Run a comparison again to collect fresh Docker evidence, or download its JSON report. Those proposed fixes are tested in disposable copies; the dashboard does not apply them to the original repositories.

| Case | What was measured | Scope |
|---|---|---|
| GPU energy recommender: pandas 2.3.3 → 3.0.0 | Two actual data-collection functions pass before the upgrade and fail on `freq='H'` afterward; lowercase `h` passes on both versions. | Repository source at `7a802eace3a3775acc5ea4af6c266079e2599eb3`, with a prepared two-function probe. The deployed pandas version is unknown. |
| Customer import: Pydantic 1.10.18 → 2.8.2 | Existing tests pass twice; omitting `nickname` fails only after the upgrade; an explicit `= None` default passes on both versions. | A deliberately small app fixture and prepared missing-field probe. |

Dashboard comparisons use the prepared probes and cached Docker images with network access disabled inside the test containers. They require Docker to be running and the case's local artifacts and images to be available. Pydantic image preparation is documented below; this workspace also contains the GPU audit's prepared images and evidence. Missing setup or unexpected test outcomes must remain visible as unavailable or inconclusive.

The regular dashboard comparison uses prepared test data and runs Docker locally. For the Pydantic case, **Run live investigation** fetches the source through Bright Data, retrieves any prior matching Cognee finding, asks Strands/Groq for constrained test data, runs the Docker comparisons, then stores and retrieves the confirmed finding through Cognee. Separate service cards show what actually succeeded; displaying a saved report does not repeat those calls. **Confirmed break** describes the measured code behavior, while the service evidence separately reports retrieval and persistence success or failure. **Inconclusive** means the comparison could not establish that result; it is not an upgrade-safety verdict. The pandas case remains a prepared Docker comparison without live service integration.

The [21-repository audit](reports/dependency-audit-2026-09-21.md) was a bounded, agent-assisted inspection performed during development. Its deeper manual investigations are separate from the dashboard's supported static rules. The audit report records other findings and their limits.

## Dependency-upgrade demo

Python 3.12, the installed `requirements.txt`, and Docker are required. Build the two environments once (downloads public pinned dependencies, no API keys required):

```sh
python -m src.main upgrade-demo --prepare
```

Run the full investigation using `BRIGHTDATA_API_KEY` and `GROQ_API_KEY` from `.env`:

```sh
python -m src.main upgrade-demo
```

For the sponsor demonstration, require both real integrations with no direct-source fallback:

```sh
python -m src.main upgrade-demo --require-integrations
```

This mode returns 2 if Bright Data fails to supply verified source evidence, or if Cognee storage/retrieval fails. The same mode powers the dashboard's live button. It cannot be combined with offline, image-preparation, or no-memory modes.

The command compares the requirements files, locates `Customer.nickname` in the app's AST, reads the versioned Pydantic migration guide through Bright Data, and asks Strands/Groq for a source-supported missing-input test case. If Bright Data fails or returns no matching source evidence, the command tries a bounded, credential-free HTTPS read of the same fixed upstream URL. The report labels that fallback; neither live path silently substitutes the curated offline note. A trusted renderer turns the proposed input/expected output into a unittest; the model never supplies executable Python or shell commands. The prepared fix is explicit and specific to this documented change.

Six isolated Docker runs establish the evidence: existing tests on both versions, the identical generated probe on both versions, and that probe against the fixed app on both versions. A confirmed finding requires the original tests to pass twice, the probe to pass before the upgrade and fail with the expected missing-nickname validation error afterward, and the fix to pass twice. Actual installed versions are checked; image IDs, app/test hashes, source provenance, test output, and the fix patch are saved under `.commit-watch/upgrade-demo/<run>/`.

Tests run as an unprivileged user with no network, no credentials, a read-only app/test mount, and a timeout. Package installation occurs only when building images. A completed comparison returns **1** when a behavior break is confirmed and **2** when evidence is inconclusive or setup fails. Image preparation returns **0**.

For a repeatable presentation without API calls, use the curated source note and prepared probe data with the already-built images:

```sh
python -m src.main upgrade-demo --offline
```

Offline mode is labeled in the report, requires cached images, and does not access Cognee memory. Use `--prepare --rebuild` to rebuild the environments. Live runs store only confirmed findings and the verified workaround in a separate Cognee compatibility dataset, then query Cognee to retrieve the evidence and check its recorded hash. A small local index locates datasets; its contents never substitute for a Cognee search result. A later live run searches prior findings matching the exact app hash and version pair and supplies the retrieved evidence to the model. The tests still run again. `--no-memory` skips these steps outside strict mode. This is retrieval of verified experience, not training or autonomous learning. The report explicitly identifies persistence/retrieval failures.

### Cognee Cloud graph

The upgrade demo supports two explicit memory backends. The default `COGNEE_MEMORY_BACKEND=local` stores its graph on this computer; those nodes do not appear in Cognee Cloud. To send new verified findings to Cloud, set these values in the ignored `.env` file:

```dotenv
COGNEE_MEMORY_BACKEND=cloud
COGNEE_API_URL=https://<your-tenant>.aws.cognee.ai
COGNEE_API_KEY=<your-cloud-api-key>
COGNEE_TENANT_ID=<your-tenant-id>
```

Copy the URL and optional tenant ID from **Cognee Cloud → API Keys → Connection Details**. Keep the key in `.env`, never in the frontend or a report. Cloud mode uploads only the bounded verified evidence document, requests graph extraction, waits for completion, then verifies the exact retrieved evidence hash and positive graph node/edge counts. Only then does it publish a tenant-specific index entry under `.commit-watch/upgrade-cloud/`. It never silently falls back to local memory. The dashboard identifies local and Cloud results separately and links to the Cloud graph for Cloud receipts. The original `ingest`/`check` commands continue using local Cognee.

Run `python -m src.main upgrade-demo --require-integrations` after configuring the Cloud key. Each successful run creates a `secondlook_compatibility_…` dataset; select that dataset in the [Cloud graph](https://platform.cognee.ai/knowledge-graph). Source requests, graph processing, and service availability can extend the runtime; the dashboard keeps its seven-minute overall deadline.

A separate manual Cloud seed was uploaded through the signed-in UI on September 21: [secondlook-cloud-evidence.txt](reports/secondlook-cloud-evidence.txt), in dataset `secondlook_upgrade_evidence`. Cloud reported **22 extracted entities**, including six test-result entities and two package-version entities. The graph was subsequently verified visually in Safari: nodes and connections are displayed. This manual seed is separate from the automatic API upload/retrieval verification below. That distinction is recorded in [cloud-ui-verification.json](reports/cloud-ui-verification.json). The file's pending-upload wording describes its preparation time; the separate receipt records the subsequent UI observation.

A live API check subsequently uploaded the canonical finding to `secondlook_compatibility_1ee2039c43c540bd90f7d012df1e1574`, completed graph extraction, retrieved the exact evidence frame, and independently recalled it again. Its actual dataset graph contained **20 nodes and 30 edges**. Receipts are saved in `.commit-watch/cognee-cloud-verification.json` and `.commit-watch/cognee-cloud-prior-recall.json`. The tenant's graph-summary cache returned `computedAt: null` with zero counts, so verification reads and validates the actual dataset graph in that case; the receipt labels the count source. The Cloud API key and tenant URL are now configured locally, and the upgrade memory backend is set to `cloud`.

The complete Cloud rehearsal in `.commit-watch/upgrade-demo/20260922T024137Z-296a3fdf/` then succeeded with `--require-integrations`: Bright Data fetched the source, prior Cloud memory was recalled before the model call, all six expected Docker outcomes were measured, and the new finding was stored and retrieved from Cloud with **22 nodes and 32 edges**. The dashboard displays that verified Cloud receipt. The full regression suite passed 264 tests and 469 subtests before the final graph-summary fallback adjustment; afterward, all 102 affected Cloud, upgrade-demo, and dashboard tests passed.

The fixture's existing tests cover explicit nickname values and explicit `None`; only the new probe omits the key. The official [Pydantic 2.8 migration guide](https://pydantic.dev/docs/validation/2.8/get-started/migration/#required-optional-and-nullable-fields) explains this change. No exhaustive release-note or issue investigation is claimed.

**Verified in this workspace:** the live Strands/Groq run and the offline replay both produced the following measured results:

| Check | Pydantic 1.10.18 | Pydantic 2.8.2 |
|---|---|---|
| Existing tests | Pass | Pass |
| New missing-nickname probe | Pass | Fails with the expected validation error |
| Same probe after adding `= None` | Pass | Pass |

The first live run used the explicitly labeled direct HTTPS source fallback. The later required-integration run in `.commit-watch/upgrade-demo/20260922T020900Z-cb4ee155/` fetched the official versioned guide through Bright Data with no fallback, reproduced all six expected Docker outcomes, and stored/processed/retrieved the exact finding through Cognee. A separate real Cognee search subsequently recalled that dataset; its result is `.commit-watch/cognee-prior-recall-smoke.json`. Bright Data requests have intermittently failed, so strict mode allows at most one retry and reports a failed run when neither attempt returns verified evidence. This is proof of real integration, not a promise of service availability. The older offline replay is in `.commit-watch/upgrade-demo/20260922T014158Z-e473ed54/`. A failing upgrade probe and exit code 1 are the intended demonstration result; strict integration failure returns 2 even if the code comparison succeeded.

The dashboard live rehearsal in `.commit-watch/upgrade-demo/20260922T021505Z-c34945a8/` also succeeded: Bright Data supplied fresh source evidence, Cognee recalled the prior exact-scope finding before the model call, the six comparisons reran, and the new finding was stored and retrieved. This used the corrected 60-second Bright Data request budget (the earlier 30-second budget caused observed cancellations). Strict mode still permits only two attempts and the dashboard caps the full run at seven minutes. At that local-rehearsal stage, the regression suite reported **202 tests and 365 subtests passed**; subsequent Cloud verification is documented above.

## Original commit-watch review

A second pair of eyes for teams that push straight to `main`. Store 30–50 commits in local Cognee, retrieve the repo's usual patterns, judge one new commit with Strands and Groq, then show a Docker test run alongside the judgment. This workspace is named `secondlook`; the application is `commit-watch`.

The four criteria are message/size mismatch, out-of-place files, logic without tests, and a clear break from baseline patterns. Judgment is conservative and validated as JSON. The test result is separate evidence: a passing test does not erase a flag. Dependency changes can also receive sourced upstream context through **Bright Data → Strands → Cognee**.

**Setup**

Use Python 3.12, Git, and Docker with its daemon running. From this directory:

```sh
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
docker build -t commit-watch-sandbox:latest -f sandbox/Dockerfile .
```

Create `.env` from `.env.example` if it is missing; preserve any existing credentials. Set `GROQ_API_KEY` using a [Groq API key](https://console.groq.com/keys). `.env` is ignored by Git.

The supplied configuration targets `Eman-Gon/fp-multimodel`, stores 35 baseline commits, and runs `python -m pytest -q -p no:cacheprovider && npm test` with a 60-second limit. `GITHUB_TOKEN` needs repository Contents read access. Public repos also work without a token, subject to GitHub's lower rate limit. The application reads `GITHUB_TOKEN`; an authenticated GitHub CLI session alone does not configure it. To reuse that session without printing the token:

```sh
export GITHUB_TOKEN="$(gh auth token)"
```

These original commit-review commands use local Cognee and download the BGE embedding model/tokenizer on first use (already cached in this workspace). They do not use `COGNEE_API_KEY`; the upgrade demo's optional Cloud backend above does. Cognee's extraction and the judge explicitly use Groq's `openai/gpt-oss-120b` through LiteLLM (`groq/openai/gpt-oss-120b`); no AWS credentials are needed. This replaces `llama-3.3-70b-versatile`, which Groq retired for free/developer accounts. Cognee uses native structured output; the judge uses JSON object mode with strict local validation. Baseline commit metadata is sent to Groq during ingestion; checking sends the selected commit's full diff, metadata, file paths, and retrieved baseline. No full diffs are stored as baseline memory. Ingestion is paced at six extraction requests per minute by default; allow several minutes before the demo. Cognee's `LLM_RATE_LIMIT_REQUESTS` and `LLM_RATE_LIMIT_INTERVAL` environment settings can be adjusted for your account's limits.

**Run the demo**

Ingest the history ending immediately before the clean demo commit:

```sh
python -m src.main ingest --count 35 --ref 546ea04c77287d14b499fa1d985ec754673b5054
python -m src.main check 3b6302e76d0b87ca49c77b98323596d329008a13
```

General commands are `python -m src.main ingest [--count 30..50] [--ref SHA]` and `python -m src.main check SHA`. Omitting `--ref` uses the target's default branch. Ingest before pushing the commits you want to demonstrate, or explicitly select their earlier baseline ref.

Each ingestion uses a fresh Cognee dataset and publishes `.commit-watch/baseline.json` only after processing succeeds. Checks use that dataset, reject commits already in the baseline, and require its head to be an ancestor of the checked commit. Checks do not add new commits to memory.

`demo/rough.patch` prepares an intentional boundary-condition bug and unrelated scratch notes without adding tests. Its application has been checked against the clean demo commit. No rough commit or SHA has been created. In a checkout of the target repo, create a separate demo branch from the clean SHA, apply and inspect this patch, then manually commit it with the vague message `fix` and push that branch so the GitHub API can fetch it. Run `check` with the resulting SHA. Keep this deliberately broken change off `main`.

For the two-minute presentation, show the ingested baseline, check the clean SHA, check the prepared rough SHA, and show the captured test output. Rehearse both before presenting; do not assume the model's flag or test result in advance.

Exit codes:

- `0`: no flag and tests passed.
- `1`: a flag or a test failure.
- `2`: configuration, provider, sandbox, or timeout error; no successful review is implied.

**Verification and limits**

The selected clean commit passed 87 Python and 107 TypeScript tests locally and passed both suites in Docker (3.12 seconds). Applying `demo/rough.patch` only inside a disposable container produced the expected failing `test_utterance_requires_forward_time_range` test; this was a mutated-snapshot probe, not a committed rough SHA. A two-second timeout probe returned `timeout` and cleaned up its container. Evidence is saved under `.commit-watch/` in `clean-sandbox.json`, `rough-patch-sandbox.json`, and `timeout-sandbox.json`.

Tests cover the installed Strands/Cognee adapters with mocked provider responses, and the actual Strands MCP bridge talking to an in-process test server. Live smoke checks have also verified the updated Groq judgment and Cognee structured-output adapters, Bright Data search-response decoding, and reading a known public release page. The constrained live dependency search returned no matching source; that is reported separately from connection or parsing errors. Evidence is stored under `.commit-watch/`. The original 35-commit baseline ingestion hit Groq's token-per-minute limit and did not publish a baseline; the clean/no-flag and rough/flagged commit demo has **not** been rehearsed end to end. The separate dependency-upgrade demo above does not depend on that baseline. No commits have been made. Run the tests with:

```sh
python -m pip install -r requirements-dev.txt
python -m pytest tests -q
```

The sandbox image installs dependencies at build time. Its Node manifests are pinned to the clean demo SHA, and its Python dependencies match this target. Adapt and rebuild the image when changing repositories or checking dependency changes. Check-time containers have no network access and do not install packages. The report captures the test exit code and last 30 output lines; timeouts are errors. Oversized diffs and truncated GitHub trees fail rather than silently dropping evidence.

**Dependency context**

Set `BRIGHTDATA_API_KEY` in `.env` to enable the existing `check SHA` command's upstream lookups. It compares complete dependency files against the first parent and uses only actual package/version changes. A touched file with unchanged dependencies makes no web calls. Removals alone need no fresh upstream lookup.

Supported inputs are `package.json`, npm lockfiles v1–3, requirements text files, PEP 621 `pyproject.toml`, and uv/Poetry/Pipfile lockfiles. Yarn/pnpm locks, dynamic Python dependencies, and Poetry manifest dependencies are explicitly reported as unsupported; Poetry's resolved lockfile is supported. Local paths, Git sources, and direct URL declarations are not sent to search. Snapshots are limited to 256 KB, with up to eight dependency files examined per review. Multiple npm/Python source files for the same package are deduplicated, preferring changed lockfile versions; direct dependencies get lookup priority.

At most three added/changed packages receive lookups, each using Bright Data's `search_engine` then `scrape_as_markdown` through Strands' MCP client. No additional LLM judgment is made. Source selection is deliberately limited to public GitHub release/issue pages whose repository names match the package; scoped npm names also require a matching owner. This can miss legitimate upstreams with different names and does not establish official ownership. The judge treats pages as candidate evidence and must check package/version relevance. Version constraints stay intact in review/memory; search extracts version numbers to avoid searching literal constraint syntax. No Pro mode or browser tools are enabled.

The hosted MCP URL includes only the authentication token; allowed tools are restricted locally in both Strands and the call guard. The hosted `tools` query parameter suppressed the page-reading tool in live checks. Hosted security envelopes around results are decoded only when their framing IDs match; extracted content remains untrusted evidence.

The single Groq judgment returns the original three fields plus `dependency_notes`. A selected note has a supplied `source_id`, a one-line `summary`, and a verbatim `evidence_quote` that is checked against the fetched excerpt. The report includes the URL and fetch time. A dependency warning is not automatically a review flag: the same four criteria still apply. The report distinguishes unavailable lookups from retrieved sources with no selected finding.

Selected notes are saved in separate Cognee datasets, indexed in `.commit-watch/dependency-memory.json`. Recall matches the exact ecosystem/package/version and only uses notes fetched within seven days; it returns up to two sources per package and six total. The index keeps 100 recent source entries. Older datasets are not deleted automatically. These notes never modify the baseline manifest or store review flags/test outcomes as baseline facts. Cognee note ingestion uses its own graph-extraction calls after the single judgment, so saving notes can add latency. Missing keys, lookup errors, and recall/storage failures are visible but do not stop the core review. Web excerpts and selected notes may be sent to Groq; credentials stay outside the prompt and sandbox.

Optional recall and persistence each have a 20-second deadline. If upstream context would exceed the judgment's prompt budget, it is omitted with a visible warning while the complete commit and baseline evidence remain intact.

The original commit-review flow has manual triggers and a terminal report. The local dashboard supports public repository checks and two prepared compatibility comparisons. Baseline correction, webhooks, and automatic before/after test generation for arbitrary repositories are not implemented.

**Upstream API references**

- [GitHub commit metadata, pagination, and diff media types](https://docs.github.com/en/rest/commits/commits)
- [Strands LiteLLM provider](https://strandsagents.com/docs/user-guide/concepts/model-providers/litellm/) and [provider API](https://strandsagents.com/docs/api/python/strands.models.litellm/)
- [LiteLLM Groq provider](https://docs.litellm.ai/docs/providers/groq)
- [Groq GPT-OSS 120B](https://console.groq.com/docs/model/openai/gpt-oss-120b) and [model deprecations](https://console.groq.com/docs/deprecations)
- [Bright Data MCP tools](https://docs.brightdata.com/products/mcp-server/tools) and [server source](https://github.com/brightdata/brightdata-mcp)
- [Cognee add](https://github.com/topoteretes/cognee/blob/main/cognee/api/v1/add/add.py), [cognify](https://github.com/topoteretes/cognee/blob/main/cognee/api/v1/cognify/cognify.py), and [search](https://github.com/topoteretes/cognee/blob/main/cognee/api/v1/search/search.py)
- [Cognee result serialization and dataset isolation](https://github.com/topoteretes/cognee/blob/main/cognee/modules/search/methods/search.py), [LiteLLM routing](https://github.com/topoteretes/cognee/blob/main/cognee/infrastructure/llm/structured_output_framework/litellm_native/get_native_client.py), and [local embeddings](https://github.com/topoteretes/cognee/blob/main/cognee/infrastructure/databases/vector/embeddings/get_embedding_engine.py)
