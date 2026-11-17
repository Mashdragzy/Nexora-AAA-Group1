# What we built

AAA Group 1, ESSEC SPOC AI final assignment (Advanced AI Algorithms)

## Starting point

We started from the public GitHub project **Cendro** (Team Kryptosphere, Ledger N3XT Hackathon 2026, MIT license): https://github.com/kryptosphere-students/N3XT-Hackathon-26. It already contained the AI intake agent, the mandate check, the netting engine and the settlement contracts, but it could only be demonstrated with a Ledger Flex hardware wallet and a manual setup. We renamed our version of the application **Nexora**.

The assignment asks for an application built with existing tools, that is reproducible from the code and explained through a demo. Our work focused on making Nexora **reproducible by anyone, easy to understand on first use and verifiable**.

## Our changes

| Area | Before | After |
|---|---|---|
| Reproducibility | needed a Ledger Flex and three addresses in `.env` to sign anything | **demo mode**: "Use demo accounts" signs with local test accounts; works on any computer |
| Setup | several manual steps, no prerequisite check | **`npm run demo:easy`**: checks Node and Foundry, starts chain, contracts, server and dashboard, opens the browser |
| Design | one long dark page with six blocks | light theme, **four tabs** and a live **overview**: four-stage flow (AI, code, human, blockchain) with counters, key figures, gross against net chart, latest activity |
| First use | six blocks, no explanation of the flow | **4-step guide** that opens the right tab, a one-line explanation on every block and a "next step" button on each tab |
| AI agent cost | paid models | **free models** by default (InclusionAI Ling 3.2, with NVIDIA Nemotron 3 Ultra as fallback), checked live on the three demo emails |
| AI agent without a key | red badge "Agent key: none", buttons failing with a server error | clear notice: the agent is off, what still works, how to enable it |
| Messages | raw amounts ("105010500 USDC"), wording tied to the Ledger | readable amounts, neutral wording ("Signed", "Approved by") for both signers |
| Verification | screenshot script for the original README | **`npm run demo:walkthrough`**: end-to-end check in Chrome (demo accounts, mandates, orders, refused order, netting, approvals, settlement, final overview) with one screenshot per step |
| AI reliability | an empty or busy model answer ended in a server error | automatic switch to the fallback model on empty answers, readable message when the free models are busy (3 new tests) |
| Demo video | recorded by hand | **`npm run demo:video`**: the video records itself (scenario in Chrome, subtitles, synthetic voice-over, live AI agent) |
| AI evaluation | three demo emails | **AI test bench (red team)**: eight synthetic emails in English and French (clear, ambiguous, three prompt injections, an order for another manager, an order above the cap), each run through the real agent and the signed mandate, with a verdict and a score |
| Refusals | technical reasons ("105000000 USDC is above the signed cap...") | **plain-language explanation** of every refusal, tested in the engine |
| Audit | data only in JSON files | **audit trail export (CSV)**: every signed decision in time order, with signer and signature or transaction hash |
| Code readability | comments pointing to an internal backlog ("S-026", "D11") | explicit comments explaining each rule |
| Documentation | hackathon README | user guide with a guided tour, troubleshooting and limits; this file; AI usage and sources |

All tests pass (128 passed including our 13 new ones, 0 failed, 3 skipped), lint and type checks are clean and the walkthrough runs from start to settlement. The full list of changed files is in the git history of the code (`git log`).

## Files we added or changed

- `apps/web/src/ledger/demo.ts` (new): demo signer on anvil test accounts 2 to 4
- `apps/web/src/ledger/dmk.tsx`, `sign.ts`: one signer interface for Ledger and demo accounts
- `apps/web/src/components/Overview.tsx`, `Tabs.tsx`, `GettingStarted.tsx`, `useDashboard.ts` (new): tabs, live overview and first-visit guide
- `apps/web/src/App.tsx`, `App.css` and the components: block explanations, notices, wording
- `scripts/demo-easy.mjs`, `scripts/demo-walkthrough.mjs`, `scripts/demo-video.mjs` (new), `package.json` scripts
- `apps/server/src/agents/intake.ts`, `routes/intake.ts`: fallback on empty answers and readable errors
- `apps/server/src/routes/bench.ts`, `routes/audit.ts`, `apps/server/demo/emails/*` (new): AI test bench, audit trail export, five new test emails
- `packages/engine/src/explain.ts` (new): plain-language explanation of refusals
- `apps/web/src/components/BenchPanel.tsx` (new): AI test bench tab
- comments in `apps/server`, `packages/engine`, `packages/protocol`
- `README.md` (new; the original one is kept as `ORIGINAL_PROJECT_README.md`)
