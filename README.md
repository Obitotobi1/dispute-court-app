# DisputeCourt App

A web app for **DisputeCourt**, a GenLayer Intelligent Contract that resolves two-party disputes with validator consensus.

Justice is slow, expensive and wrong. AI agents will soon make millions of deals that no court can keep up with. DisputeCourt gives them (and people) a cheap, neutral way to settle a disagreement and build a public track record.

- **Live app:** https://obitotobi1.github.io/dispute-court-app/
- **Contract:** `0x6D8d8fe14Ddcd7E5F8A69902Fcab7D05090834Fb` (GenLayer Studio)
- **Contract source:** [`dispute_court.py`](./dispute_court.py) in this repository
- **Tests:** [`tests/test_dispute_court.py`](./tests/test_dispute_court.py) in this repository
- Also published with its own history at https://github.com/Obitotobi1/dispute-judge

## What the app does

1. **Open a case.** The claimant writes a title, a claim and an optional rubric, and names a respondent.
2. **Respond.** Only the named respondent can submit Side B.
3. **Judge.** Validators each run an LLM and must agree on a structured verdict (`SIDE_A`, `SIDE_B` or `SPLIT`, plus confidence 1-5) using `gl.eq_principle.prompt_comparative`.
4. **Appeal once** with new evidence, or **accept the verdict**.
5. **Record.** Final verdicts update per-address win/loss counts that anyone (or any contract) can read.

Every write shows the full transaction lifecycle: signed, submitted, accepted by consensus, finalized.

## Contract methods used by the app

| App action | Contract method | Who can call it |
|---|---|---|
| Open a case | `open_case(title, claim_a, respondent, rubric)` | anyone (becomes claimant) |
| Respond | `respond(case_id, claim_b)` | the named respondent |
| Ask validators to judge | `judge(case_id)` | anyone, on a `RESPONDED` case |
| Appeal | `appeal(case_id, new_evidence)` | claimant or respondent, once |
| Accept verdict | `finalize(case_id)` | claimant or respondent only |
| Case list and detail | `get_case(case_id)` | read only |
| Win/loss lookup | `get_record(address)` | read only |

## Protections (and how they are tested)

- **Appeals cannot be cut off.** Only the two parties can call `finalize`, and a case closes only when the side that would lose its appeal accepts (the respondent after `SIDE_A`, the claimant after `SIDE_B`, both after `SPLIT`). The winner cannot close it alone. The app shows the accept button only to the parties and explains who must still accept.
- **Prompt isolation.** The title, rubric, both claims and the appeal text are each fenced in their own tag with `<` and `>` escaped, so none of them can close a tag or inject instructions. Invalid model output falls back to `SPLIT`.

Run the tests (no node needed, they run the contract logic against a small stand-in for the `genlayer` library):

```
python3 tests/test_dispute_court.py
```

## Try it alone in two minutes

The app generates two demo accounts in your browser (no wallet, free on Studio).

1. Open the app and click **Open a case**. Click **Use account B** for the respondent, fill in a claim, and send.
2. Switch **Acting as** to **Account B**, open the case, and submit a response.
3. Click **Ask validators to judge** and wait for consensus.
4. Switch to **Account A** and appeal with new evidence (or, as the losing side, accept the verdict). Then check **Win/loss record**.

## Run locally

It is a single static file. Open `index.html` through any static server, for example `python3 -m http.server`, then visit `http://localhost:8000`.

## Notes

- The SDK (`genlayer-js`) is loaded from a CDN at runtime.
- Demo keys are stored in your browser's local storage and are only for the Studio test network. Do not reuse them anywhere valuable.
- Case text is escaped before display.
