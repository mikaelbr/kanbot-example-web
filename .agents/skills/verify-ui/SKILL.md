---
name: verify-ui
description: Run the app and use Playwright to screenshot key UI states at 390px and 1280px, and check console errors. Use after building or fixing a UI change, when reviewing a UI change, or when asked to verify, screenshot or visually check the app. Saves to the directory you are given, or a temp directory. Degrades gracefully when there is no UI or dev server.
---

# Verify UI

Screenshots let a reviewer see the change without running it. Capture each state the task lists, at mobile and desktop width, and look at them yourself first.

## 1. Decide if this applies

No visual change → skip and say "no UI to verify". A UI but no way to run it (no dev script, needs secrets or services you lack) → say what is missing and rely on component tests.

## 2. Output directory

Use the directory the caller gives you. If none is given, make a temp directory outside the repo:

```bash
OUT="${OUT:-$(mktemp -d)}"
```

Do not commit screenshots unless the caller asks.

## 3. Start the app

1. Get the dev command, port and env from `AGENTS.md` or the project's conventions. Otherwise look at the scripts in the manifest (`dev`, `start`, `preview`).
2. Install dependencies with the project's package manager if they are missing.
3. Start it in the background, log to a file, and wait until it responds:

```bash
(<dev command> > "$OUT/dev.log" 2>&1 &)
for i in $(seq 1 60); do curl -sf "$BASE_URL" >/dev/null && break; sleep 1; done
```

4. Not up within ~60s → read the log, try one reasonable fix (missing env default, seed step), then stop and report.

## 4. Get Playwright

Use the project's Playwright if it has one (`@playwright/test`, a `playwright.config.*`). Otherwise run it ad hoc with the project's package runner (`npx`, `bunx`, `pnpm dlx`…) and install Chromium once. Do not add Playwright as a dependency unless the task asks.

No browser available (offline, sandbox) → say so and continue without screenshots.

## 5. Capture states

Write a throwaway script outside the repo:

```js
import { chromium } from "playwright";
const [base, outDir] = process.argv.slice(2);
const states = [
	{ name: "filled", path: "/", setup: async (page) => {} },
	{ name: "empty", path: "/?fixture=empty", setup: async (page) => {} },
	// reach error/dialog states in setup: route interception, clicks, etc.
];
const browser = await chromium.launch();
const problems = [];
for (const width of [390, 1280]) {
	const page = await browser.newPage({
		viewport: { width, height: width === 390 ? 844 : 800 },
	});
	page.on(
		"console",
		(m) =>
			["error", "warning"].includes(m.type()) &&
			problems.push(`${width} ${m.type()}: ${m.text()}`),
	);
	page.on("pageerror", (e) =>
		problems.push(`${width} pageerror: ${e.message}`),
	);
	for (const s of states) {
		await page.goto(base + s.path, { waitUntil: "networkidle" });
		await s.setup(page);
		await page.evaluate(() => document.fonts.ready);
		await page.screenshot({
			path: `${outDir}/${s.name}-${width}.png`,
			fullPage: true,
		});
	}
	await page.close();
}
await browser.close();
console.log(problems.join("\n") || "no console problems");
```

Run it from a directory where `playwright` resolves. Use `page.route()` to fake error and loading states instead of breaking the app.

## 6. Look at them

Open each screenshot and check:

- [ ] Nothing overflows or scrolls sideways at 390px
- [ ] No clipped, overlapping or truncated text that hides meaning
- [ ] Alignment and spacing match the surrounding UI
- [ ] Contrast readable; focus visible where captured
- [ ] Empty, error and loading states look intentional
- [ ] No console errors or uncaught exceptions from the change

Fix what you find, recapture, then report.

## 7. Report

- Each screenshot path, by state and width.
- Console problems.
- What you could not verify, and why.

## 8. Clean up

Stop the dev server you started and delete the throwaway script. Leave no processes running.
