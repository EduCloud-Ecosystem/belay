<p align="center">
  <img src="assets/readme/belay-hero.png" width="100%" alt="A belay rope connects a notebook and conversation to a protective gate, illustrating supported learning with safety boundaries">
</p>

<h1 align="center">Belay</h1>

<p align="center"><strong>An AI peer tutor that helps learners think, while a deterministic governance gate guards against revealing solutions.</strong></p>

<p align="center">
  <img src="assets/readme/badge-python.svg" alt="Python">
  <img src="assets/readme/badge-javascript.svg" alt="JavaScript">
  <img src="assets/readme/badge-html5.svg" alt="HTML5">
  <img src="assets/readme/badge-css.svg" alt="CSS">
</p>

<p align="center"><a href="#quickstart">Quickstart</a> · <a href="ARCHITECTURE.md">Architecture</a> · <a href="docs/authentication.md">Authentication</a> · <a href="CONTRIBUTING.md">Contributing</a></p>

## What it is

Belay is a self-hostable peer tutor. In the reference data-science pack, a learner can run code for an exercise, ask Sol for feedback, and save a conversation. A domain pack supplies the curriculum, grader and evidence for detecting solution leaks. The tutor plans a teaching move, drafts a reply and checks it against the selected stance. A final governance check can block or revise what the learner sees.

## Quickstart

### Preview the learner interface

From the repository root, use Node.js and Python 3 to serve the bundled example:

```sh
node frontend/tests/build-feedback-fixture.cjs
python -m http.server 5173 --bind 127.0.0.1 --directory frontend
```

Open <http://127.0.0.1:5173/tests/fixtures/generated-feedback-preview.html>. Select an exercise and choose **Run code** to see sample feedback. This preview uses simulated requests and responses. Stop the server with Ctrl+C.

### Run the tutor locally

For a working API, frontend and worker, follow the [controlled runtime setup](deployment/controls/README.md). The guide includes first-time preparation with Python 3.12, a synthetic learner identity and the local model stub, plus the commands to start and stop a prepared installation. Running learner code requires the [sandbox container image](backend/Dockerfile.sandbox).

For the API on its own, run `docker compose up --build` from the repository root. Check <http://localhost:8000/healthz> for liveness. The [Compose configuration](docker-compose.yml) defaults to SQLite and points inference at an OpenAI-compatible endpoint on the host. Protected requests require the [authentication configuration](docs/authentication.md). The optional PostgreSQL service also requires setting the tutor’s `DATABASE_URL` to its PostgreSQL connection string.

## Access and resource limits

The host supplies a short-lived signed token. Belay checks the learner’s institution, class and assignment version against operator-managed grants. Legacy grants allow the `study/peer` pair. Other mode and stance pairs require explicit assignment permission. See the [assignment policy](docs/assignment-pedagogy-policy.md).

The controlled runtime shares a deployment budget across providers and model tiers. An operator can configure fixed short and weekly windows using explicit UTC anchors. Both windows, the cumulative budget and the applicable scope limits must allow a request. A new window preserves prior spending and outstanding reservations. The repository prescribes no regional schedule or student allowance. See the [budget policy](deployment/controls/budget-policy.md).

Live identity binding, durable revocation, deletion and retention rules, and authoritative Portage accounting are tracked in [issue #22](https://github.com/EduCloud-Ecosystem/belay/issues/22).

## Configure inference

The local setup guide uses `local_stub` for synthetic feedback. For inference, choose `openai_compatible` or `anthropic` and configure the model tiers and credentials for that provider. A metered controlled installation also requires reviewed price bounds. See the [provider configuration](deployment/controls/controlled-runtime.md). The Bedrock adapter has no inference implementation.

## Extend and test Belay

- **Domain packs.** Start with the [skeleton pack](backend/app/packs/_skeleton/), implement the [domain contract](backend/app/core/domain/pack.py), register the factory in the [pack registry](backend/app/core/registry.py), and select it with `TUTOR_PACK`. The [data-science pack](backend/app/packs/datascience/) includes exercises, grading and a [retrievable corpus](backend/app/packs/datascience/knowledge/README.md).
- **Host integration.** The [Quad sidecar](backend/app/integrations/quad/) exposes capabilities and tutor turns under `/quad/v1`. The [frontend guide](frontend/README.md) describes the reference widget and development client.
- **Verification.** See the [validation guide](VALIDATION.md) for the backend test environment and the [frontend guide](frontend/README.md) for browser-script tests. From `backend/`, run `python -m evals.behavioral --help` for behavioral benchmark options.

## License

The [domain contract](backend/app/core/domain/LICENSE) is licensed under Apache-2.0. The [implementation](LICENSE), including the tutor, runner and reference pack, is licensed under AGPL-3.0-only. See [Licensing](LICENSING.md) for the boundary and corpus terms.
