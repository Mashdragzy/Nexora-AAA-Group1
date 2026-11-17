# AI usage, prompts and sources

AAA Group 1, ESSEC SPOC AI final assignment (Advanced AI Algorithms)

## 1. Origin of the code

Our application is built on the public GitHub project **Cendro** by Team Kryptosphere (Ledger N3XT Hackathon 2026):
https://github.com/kryptosphere-students/N3XT-Hackathon-26, MIT license.

The assignment encourages building on existing code from GitHub, Hugging Face or Kaggle, with citation. The original license and README are kept in the code (`LICENSE`, `ORIGINAL_PROJECT_README.md`). What we changed is listed in `05_What_we_built.md` and in the git history (the first commit is the unmodified import, the next ones are our work).

## 2. AI inside the application

| Component | Model | Role | Limits we rely on |
|---|---|---|---|
| Order intake agent | `inclusionai/ling-3.2-flash:free` through OpenRouter, fallback `nvidia/nemotron-3-ultra-550b-a55b:free` (both free) | reads a fund order email and calls one tool, `draft_instruction` (participant, fund, side, units); when a field is vague or missing it drafts nothing and says what is missing | it cannot sign, compute amounts or settle; every draft goes through the deterministic mandate check and a human signature |

The agent is optional: without an `OPENROUTER_API_KEY` the dashboard says the agent is off and the rest of the flow works. The demo emails include a prompt-injection attempt (`trapped.txt`) to show that the safety comes from the signed mandate, not from the model.

## 3. AI used to build the submission

| Tool | Used for |
|---|---|
| Claude Code (Anthropic, model Claude Opus 5.5), in VS Code | reading and running the original project, demo mode without a Ledger, one-command launcher, first-visit guide, interface wording, automated walkthrough, documentation |
| ChatGPT (OpenAI) | downloading the original project from the public GitHub page of Team Kryptosphere; suggesting extra narration passages for the demo video (context of the problem, responsible AI, future of AI in finance) |
| Playwright (Chrome automation), edge-tts (Microsoft neural voice "Brian"), ffmpeg | the demo video is produced by our script `npm run demo:video`: it drives the dashboard, shows subtitles and adds a synthetic voice-over; the AI agent answers live during the recording |

Every change made with the assistant was run and checked: 128 automated tests pass and the walkthrough script clicks through the whole demo in Chrome.

## 4. Prompts

### Claude Code

Our requests to Claude Code were spoken in French through voice dictation, so the raw transcripts are long and noisy. Below are the same requests **rewritten in clean English**, in the order we gave them. The content of each request is unchanged; only the wording is tidied.

**Prompt 1: understand and run the project**
> We have to submit an AI application for our ESSEC SPOC course tonight. We are starting from the public GitHub repository of the Nexora project. Copy it into an isolated folder, install what it needs, run it locally and open it in Chrome. Then check it against the assignment instructions and tell us what is missing.

**Prompt 2: explain it in plain words**
> Explain what this application actually does, in simple terms, for someone who does not know blockchain. We do not have an OpenRouter key: what still works without it? Document how AI is used in the project.

**Prompt 3: read the assignment**
> Write a prompt we can give to a browser agent so that it reads the assignment page on Moodle and extracts every requirement: deliverables, format, grading and deadline.

**Prompt 4: check the rules on reusing code**
> The project comes from existing code. Given the assignment rules, are we allowed to submit it? What do we need to cite to be fully compliant?

**Prompt 5: improve the application**
> Improve the application. Keep it as simple and as clean as possible. Make it work for anyone without a Ledger device, add short guides for the first connection, make every screen self-explanatory and clean up the code so that everything is explicit. Keep the original features and do not break the tests.

**Prompt 6: verify**
> Run all the tests and click through the whole demo in Chrome like a first-time user would. Save a screenshot of each step and fix anything that fails.

**Prompt 7: make the dashboard look professional**
> Our dashboard looks like a developer tool. Redesign it: light theme, tabs, an overview with live key figures, a chart of gross against net cash and an activity feed. Keep every feature and keep the tests green. The AI agent must run on free models only.

**Prompt 8: produce the video automatically**
> Write a script that records the demo video by itself: it plays the whole scenario in Chrome, shows subtitles and adds a natural voice-over explaining each step, in less than five minutes.

**Prompt 9: package the submission**
> Prepare a clean and professional submission: a README, a user guide with screenshots, the data files, a description of what we built and improved, the AI usage with clean prompts and the sources with the original project cited.

**Prompt 12: make the project more original (evening of the deadline)**
> Follow the brief we wrote for a teammate: check that everything still passes, then add original improvements that keep AI at the centre, without using too much of our free model quota: a red team bench of synthetic emails in English and French with a score, a plain-language explanation of every refusal and an audit trail export. Test everything, update the documentation and list every changed file.

### ChatGPT

**Prompt 10: get the original project**
> Please show how to download a public project on GitHub in order to use it as a base for another project.

**Prompt 11: enrich the video narration**
> ChatGPT was used to draft three narration passages for the video (The challenge, Responsible AI, AI in finance). The exact prompt was not saved; the passages were reviewed and edited by the team.

The three passages ChatGPT suggested were added to the voice-over of the final video, with light edits to match our writing rules.

## 5. Packages and data

**Runtime and tools:** Node.js 24 (22 or newer works), npm workspaces, TypeScript 6, Foundry 1.9.0 (`forge`, `anvil`), Google Chrome.

| Package | Version | Use |
|---|---|---|
| fastify, @fastify/cors | 5.12, 11.0 | API server |
| viem | 2.57 | Ethereum client and EIP-191 signatures |
| zod | 4.6 | input validation |
| react, react-dom | 19.2 | dashboard |
| vite, @vitejs/plugin-react | 8.3, 6.1 | development server and build |
| @ledgerhq/device-management-kit, device-signer-kit-ethereum, device-transport-kit-web-hid, context-module | 1.11, 1.18, 1.2, 2.6 | Ledger Flex signing over WebHID |
| rxjs | 7.8 | Ledger device events |
| playwright | 1.58 | automated walkthrough in Chrome |
| eslint, typescript-eslint | 10.12, 8.71 | lint |
| @openzeppelin/contracts, forge-std | 5.6, 1.17 | token standards (ERC-20, Ownable, SafeERC20) and Solidity tests |

**Data** (all synthetic, see `03_Data/DATA_MANIFEST.md`): one mock NAV file for Fund F and three demo order emails (clear, ambiguous, trapped). No personal or real market data.
