# hot-github-integration-test

Fixture repository for the `hot.dev/github` package integration tests
(`hot test --project pkg-integration-github` in the hot repo).

The tests treat this repo as a sandbox: they read the fixtures below and
exercise the package's write surface (issues, comments, labels, pull
requests, file contents, workflow dispatch). Write tests are self-cleaning
— they close what they open — but stray test issues/PRs here are expected
noise and safe to delete.

## Layout the tests rely on

| Fixture | Used by | Contract |
|---|---|---|
| `fixtures/stable.txt` | `repos/get-content` + `decode-content` reads | Content must stay exactly as committed — do not edit |
| `fixtures/scratch.txt` | `repos/put-content` update tests | Mutable; tests overwrite it freely |
| `.github/workflows/integration-dispatch.yml` | `actions/dispatch-workflow`, `list-workflow-runs`, `get-workflow-run`, `rerun-workflow` | Must keep the `workflow_dispatch` trigger and file name |
| `pr-fixture` branch | `pulls/create-pull`, `create-review`, `list-files`, `merge-pull` | Permanent head branch; tests freshen it with `put-content` so it always differs from `main`. Do not delete after merges |

## Re-seeding

If the repo drifts (e.g. `pr-fixture` was deleted), recreate the branch from
`main` with any commit touching `fixtures/pr-fixture.txt`:

```sh
git checkout -b pr-fixture main
echo "pr fixture $(date -u +%Y-%m-%dT%H:%M:%SZ)" > fixtures/pr-fixture.txt
git add fixtures/pr-fixture.txt && git commit -m "Freshen pr-fixture"
git push -u origin pr-fixture
```
