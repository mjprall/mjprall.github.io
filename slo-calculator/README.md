# SLO and error budget calculator

Live demo: [https://mjprall.github.io/slo-calculator/](https://mjprall.github.io/slo-calculator/)

An availability target is a budget of failure. This page turns an SLO into downtime and bad requests, then runs the multi-window burn-rate alerts from the [SRE workbook](https://sre.google/workbook/alerting-on-slos/) on a synthetic incident, next to the same threshold on the long window alone.

There is no build step. Python runs in the browser with [PyScript](https://pyscript.net/) 2026.7.3. `slo.py` is the budget math and the alert simulation, `charts.py` turns that into SVG, and `app.py` is the only module that touches the page.

Lookbacks scale with the SLO window (`window / 30`). The burn multipliers stay 14.4×, 6×, and 1×. A 30-day window matches the workbook (1 hour / 5 minutes, 6 hours / 30 minutes, 3 days / 6 hours). Any other window still spends about 2%, 5%, and 10% of its budget.

Paths are relative (`styles.css`, `fonts/…`, `./app.py`), so the page works when GitHub Pages serves this project at `/slo-calculator/`.

Fonts are [Newsreader](fonts/NEWSREADER-OFL.txt) and [IBM Plex](fonts/IBM-PLEX-OFL.txt), both under the SIL Open Font License.

## Preview

From this directory:

```bash
python3 -m http.server
```

Open `http://127.0.0.1:8000/`. To check the project-site path, serve the parent folder instead and open `http://127.0.0.1:8000/slo-calculator/`.

## Tests

```bash
python3 -m pip install -r requirements-dev.txt
python3 -m pytest -v
```

## Publish

One time: **Settings → Pages → Source: GitHub Actions**.

`.github/workflows/pages.yml` runs the tests on Python 3.11, 3.12, and 3.13, then deploys the page on every push to `main`. `.github/workflows/tests.yml` runs the same suite on pull requests.
