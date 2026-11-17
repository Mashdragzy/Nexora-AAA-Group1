# Nexora: start here

ESSEC SPOC AI, final assignment (Advanced AI Algorithms), AAA Group 1

**Nexora turns a fund order email into a settled trade: the AI drafts, the code checks, a human signs, the blockchain settles.** It runs on any computer in two commands, without a Ledger device or an API key.

| File | Content |
|---|---|
| `01_Nexora_Demo.mp4` | video demo, recorded automatically by `npm run demo:video` with the AI agent answering live |
| `02_Nexora_User_Guide.pdf` (and `.md`) | what Nexora does, installation, guided tour with screenshots, troubleshooting, limits |
| `03_Data/` | the data the app uses (mock NAV file, three demo emails) and its manifest |
| `04_AI_Usage_and_Sources.pdf` (and `.md`) | AI inside the app, AI used to build it, prompts, packages, origin of the code |
| `05_What_we_built.pdf` (and `.md`) | what we started from and what we improved |
| `06_Code_Nexora.zip` | full source code, with its README, tests and license |

## Run it

Requirements: Node.js 22 or newer, Foundry (`anvil`, `forge`) and Chrome.

```bash
# unzip 06_Code_Nexora.zip, then:
cd nexora/clearing-house-web3
npm install
npm run demo:easy
```

The dashboard opens on http://localhost:5173, on the Overview tab: follow the 4-step guide at the bottom, starting with **Use demo accounts** in the Participants tab. `npm test` runs the 128 automated tests and `npm run demo:walkthrough` replays the whole demo in Chrome.

## Origin

Built on the public GitHub project Cendro by Team Kryptosphere (https://github.com/kryptosphere-students/N3XT-Hackathon-26, MIT license), as the assignment allows. Details in `04` and `05`.
