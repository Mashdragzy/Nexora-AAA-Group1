# Nexora: User Guide

ESSEC SPOC AI, final assignment (Advanced AI Algorithms), AAA Group 1

## 1. What Nexora does

Today a fund subscription or redemption starts as an **email**. Someone retypes it, waits for the NAV; cash and units move two or three days later. Typing errors happen and the only proof that the manager approved the order is an email thread.

Nexora handles the same order in four steps:

| Step | Who | What happens |
|---|---|---|
| 1. Draft | **AI agent** | reads the order email and drafts it (fund, side, units). It cannot sign or compute amounts. |
| 2. Check | **Code** | compares the draft with the mandate the manager signed (allowed fund, sides, cash cap per day). |
| 3. Sign | **Human** | the manager signs the exact order text (Ledger Flex or a demo account). |
| 4. Settle | **Blockchain** | the day's orders are netted and units are swapped against cash in one transaction. |

**Why it matters.** Settlement in seconds instead of T+2, no retyping, a cryptographic proof of every approval and less cash in motion: in the demo below, 14,700 of gross orders only move 6,300 (57% saved by netting). The AI saves the reading and typing work; the signed mandate makes sure a fooled or wrong AI can never move money on its own.

## 2. What you need

| Item | Required | Notes |
|---|---|---|
| Node.js 22 or newer | yes | https://nodejs.org |
| Foundry (`anvil`, `forge`) | yes | https://book.getfoundry.sh/getting-started/installation. On Windows without WSL: unzip the `foundry_*_win32_amd64.zip` release and add the folder to `PATH`. |
| Google Chrome | yes | the dashboard and the optional Ledger connection use Chrome |
| Ledger Flex | no | only to sign on real hardware |
| OpenRouter API key | no | free account, no credit card: only to run the AI agent live (block 3 of the dashboard) |

## 3. Install and start (two commands)

Open a terminal in the `clearing-house-web3` folder of the code:

```bash
npm install
npm run demo:easy
```

`demo:easy` checks Node and Foundry, starts a local blockchain, deploys the contracts, starts the server and opens **http://localhost:5173** in your browser. It takes about 20 seconds. Keep the terminal open; `Ctrl+C` stops everything.

## 4. First connection: a guided tour

The dashboard has four tabs: **Overview**, **Participants**, **Orders and AI agent** and **Settlement**. The overview shows the four-stage flow, the key figures, gross against net cash per lot and the latest activity; at the bottom, a 4-step guide opens the right tab for each step. Each numbered block starts with one line explaining what it does; a button at the bottom of each tab leads to the next one.

![Welcome screen](02_User_Guide_images/01-welcome.png)

**Participants tab, block 1: who signs.** Click **Use demo accounts**. Manager A, Manager B and Depositary C are ready: they sign in the browser with local test accounts (no real value, local chain only).

![Demo accounts](02_User_Guide_images/02-demo-accounts.png)

**Block 2, the rules.** Open **Sign a mandate**, keep the defaults (Fund F, subscribe and redeem, cap 5,000,000 per day) and click **Sign the mandate**. Do it for Manager A, then select Manager B and sign again.

![Mandates signed](02_User_Guide_images/03-mandates-signed.png)

**Orders and AI agent tab, block 4: the orders.** Sign a **Subscribe 100 units** order for Manager A and a **Redeem 40 units** order for Manager B. The black box shows the exact text that is signed. Block 5 lists every order with its timeline.

![Orders signed](02_User_Guide_images/04-orders-signed.png)

**Try a bad order.** Sign a subscription of 1,000,000 units for Manager A: it is far above the signed cap, so the code refuses it and it never reaches settlement.

![Order refused by the mandate](02_User_Guide_images/05-order-refused-by-mandate.png)

**Settlement tab, block 6.** Click **Settle cycle (netting)**, then **Sign approval** on each manager's line. The lot settles on the local blockchain: Manager A receives 100 units, Manager B delivers 40, the fund only delivers the net 60 units. Gross 14,700 against net 6,300.

![Lot settled on chain](02_User_Guide_images/06-lot-settled.png)

**Back to the overview.** The flow, the key figures (57.1% of cash saved, 3 seconds from order to settlement), the chart and the activity feed now tell the whole story of the day.

![Overview after settlement](02_User_Guide_images/07-overview-after-settlement.png)

**Orders and AI agent tab, block 3: the AI agent** (needs a free key, see section 5). The demo buttons load a clear email, an ambiguous one and a **trapped** one that hides "redeem 1,000,000 units" in a fake system notice. The agent drafts the real order; **Simulate a fooled agent** shows that even an agent that obeyed the trap would be refused by the signed mandate.

**AI test bench tab, block 7 (needs the free key).** Eight synthetic emails, in English and French, test the agent with a critical eye: two clear orders, a vague one, an order for another manager, three prompt injections (a fake system notice, a French internal note, a fake pre-approval hidden below the signature) and a genuine order above the signed cap. Click **Run** on a line: the email goes through the real agent, then through the signed mandate; the line then shows a verdict (*as expected*, *caught by the mandate*, *safe: nothing drafted* or *unsafe*). The score at the top adds them up. Each run is one call to the free model, so run only the cases you want to show. Sign the mandates of Manager A and Manager B first so that the mandate check is meaningful.

![AI test bench](02_User_Guide_images/08-ai-test-bench.png)

**Plain-language refusals.** Every refusal, in the order form, the orders table, the AI intake and the bench, is explained in one plain sentence, for example: "This order is worth 105,000,000 USDC, above the 5,000,000 USDC per cycle that the manager's signed mandate allows."

**Audit trail.** In the overview, **Download the audit trail (CSV)** saves every signed decision of the session in time order (mandates, orders with their status, lot approvals and settlement), with the signer and the signature or transaction hash. It opens directly in Excel.

## 5. Going further

**Run the AI agent.** Create a key on https://openrouter.ai, then in `clearing-house-web3`:

```bash
cp .env.example .env      # Windows: copy .env.example .env
```

Set `OPENROUTER_API_KEY=` in `.env` and restart `npm run demo:easy`. The header badge turns to "AI agent: on". The default models are free (`:free` on OpenRouter): InclusionAI Ling 3.2, with NVIDIA Nemotron 3 Ultra as fallback. Checked on 17 November 2026: the clear email gives the right draft, the ambiguous one gives none and the trapped one gives the real 10-unit order with the hidden notice called out. Models are set by `LLM_MODEL` and `LLM_FALLBACK_MODEL`.

**Sign on a Ledger Flex.** Put the three Flex addresses in `.env` (`PARTICIPANT_A_ADDRESS`, `B`, `C`), close Ledger Wallet, run `npm run demo:local` and click **Connect a Ledger Flex** in Chrome. Every signature is then confirmed on the device screen.

## 6. Check that everything works

```bash
npm test                   # 128 tests: contracts, engine, protocol, server
npm run demo:walkthrough   # clicks through the whole demo in Chrome, saves screenshots
npm run demo:video         # records the demo video with subtitles and voice-over
```

`demo:video` also needs the OpenRouter key, Python with `edge-tts` (`pip install edge-tts`), ffmpeg and `npx playwright install ffmpeg`.

Both commands were run successfully on Windows 11 with Node 24.18 and Foundry 1.9.0 on 17 November 2026. The walkthrough screenshots in this guide come from that run.

## 7. Troubleshooting

| Message | Fix |
|---|---|
| `Foundry is missing` | install Foundry and reopen the terminal (section 2) |
| `port 8545 / 8787 / 5173 is already in use` | an earlier run is still open: stop it with `Ctrl+C` or close the terminal |
| "Waiting for the Nexora server" in the page | the server is still starting: wait a few seconds |
| "AI agent: off (no key)" | expected without an OpenRouter key; everything else works |
| Ledger not found | Chrome only, Ledger Wallet closed, device unlocked with the Ethereum app open |

## 8. Limits

Nexora is a proof of concept: one fund, three participants, test tokens, a mock NAV file and JSON files as storage. The smart contract trusts the engine to have checked the approvals (the signatures are published on chain so anyone can verify them). It is not a production financial service and does not replace NAV publication, audits or regulation.
