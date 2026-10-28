# DisputeCourt App

A web app for **DisputeCourt**, a GenLayer Intelligent Contract that resolves two-party disputes with validator consensus.

Justice is slow, expensive and wrong. AI agents will soon make millions of deals that no court can keep up with. DisputeCourt gives them (and people) a cheap, neutral way to settle a disagreement and build a public track record.

- **Contract:** `0x8587f4fFa73A55bcb679f913aD478B1AfC724Ec1` (GenLayer Studio)
- **Contract source and tests:** https://github.com/Obitotobi1/dispute-judge

## What the app does

1. **Open a case.** The claimant writes a title, a claim and an optional rubric, and names a respondent.
2. **Respond.** Only the named respondent can submit Side B.
3. **Judge.** Validators each run an LLM and must agree on a structured verdict (`SIDE_A`, `SIDE_B` or `SPLIT`, plus confidence 1-5) using `gl.eq_principle.prompt_comparative`.
4. **Appeal once** with new evidence, or **finalize**.
5. **Record.** Final verdicts update per-address win/loss counts that anyone (or any contract) can read.

Every write shows the full transaction lifecycle: signed, submitted, accepted by consensus, finalized.

## Try it alone in two minutes

The app generates two demo accounts in your browser (no wallet, free on Studio).

1. Open the app and click **Open a case**. Click **Use account B** for the respondent, fill in a claim, and send.
2. Switch **Acting as** to **Account B**, open the case, and submit a response.
3. Click **Ask validators to judge** and wait for consensus.
4. Appeal with new evidence or finalize. Then check **Win/loss record**.

## Run locally

It is a single static file. Open `index.html` through any static server, for example `python3 -m http.server`, then visit `http://localhost:8000`.

## Notes

- The SDK (`genlayer-js`) is loaded from a CDN at runtime.
- Demo keys are stored in your browser's local storage and are only for the Studio test network. Do not reuse them anywhere valuable.
- Case text is escaped before display.
