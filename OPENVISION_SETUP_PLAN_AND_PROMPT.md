# OpenVision setup plan and desktop continuation prompt

Updated: 2026-10-10

Repository: https://github.com/sman007/OpenVision
Setup PR: https://github.com/sman007/OpenVision/pull/1 (MERGED 2026-10-10)
Setup branch: `feature/windows-ipa-build` (merged; later doc commits still land here)

## Verified handoff state

PR #1 is MERGED. Steps 1 through 4 are complete and verified; see **Completion record** below for the
evidence. The build is no longer speculative: it has run green and produced a checksum-verified IPA.

What remains UNVERIFIED: Windows signing and installation (step 5), glasses registration, Gen 3
compatibility, camera-source confirmation, and locked-iPhone operation (step 6). Re-check this state
when resuming rather than trusting it; the artifact in particular expires.

The user has a Windows desktop and an iPhone, with no local Mac/Xcode environment. The local checkout is `C:\Projects\OpenVision`, verified by its `origin` remote pointing at sman007/OpenVision, with `upstream` set to rayl15/OpenVision. Confirm it still exists rather than assuming it; do not invent any other local path or assume a cloud workspace is the user's desktop.

The user authorizes completing Meta configuration, adding the required repository secrets, running and fixing the build, and progressing toward installation. Desktop browser permission has been granted, but an actual desktop browser connection must be verified.

## Execution plan

1. **Verify desktop access and repository state**
   - Confirm the browser tool controls the user's actual desktop browser.
   - If unavailable, identify the environment's supported connector and provide verified setup instructions. Do not claim that opening a conversation on desktop automatically grants control.
   - Inspect repository instructions, PR #1, current workflow, project configuration, and setup documentation.
   - The checkout is recorded above. Re-verify its Git remote against sman007/OpenVision instead of searching for it again, and never guess a path.

2. **Complete Meta developer configuration**
   - Start from https://wearables.developer.meta.com/ and follow the current official Wearables developer instructions.
   - Reuse a suitable existing app or create the OpenVision developer app as needed.
   - Confirm the required Wearables configuration and any current developer/test-user or device enrollment requirements.
   - Match the project's current values. PR #1 presently generates bundle ID `com.sman007.openvisiontrial` and callback URL scheme `openvision`.
   - Obtain the Meta App ID and app-specific client token. The workflow expects the full token in `AR|APP_ID|TOKEN` format.
   - Have the user complete sign-in, two-factor authentication, and any account agreements when required.

3. **Add GitHub Actions repository secrets**
   - Open https://github.com/sman007/OpenVision/settings/secrets/actions .
   - Add `OPENVISION_META_APP_ID` and `OPENVISION_META_CLIENT_TOKEN`.
   - Keep values out of chat, commits, issues, screenshots shared in reports, and workflow logs. Never use a privileged server secret as a client token.
   - Report only that the named secrets exist, never their values.
   - Do not put an OpenAI API key into CI. Configure it locally in the iPhone app.

4. **Run and resolve the iPhone build**
   - Review PR #1 before merging. If the manual workflow is unavailable because it is absent from the default branch, resolve that prerequisite using the reviewed PR.
   - Run **Windows iPhone trial IPA** on the intended branch.
   - Verify runner availability and the installed Xcode path rather than assuming the hardcoded toolchain exists.
   - Inspect failed steps, fix the underlying issue, and rerun until a successful build or a concrete blocker.
   - Download `openvision-unsigned-iphone`, containing `OpenVision-unsigned.ipa` and `SHA256SUMS.txt`.
   - Verify the IPA checksum. The current artifact retention is seven days.
   - An unsigned IPA is not yet an installable, signed iPhone application.

5. **Sign and install on Windows**
   - Use the official Sideloadly site: https://sideloadly.io/ .
   - Follow its current Windows dependency and signing instructions with the user's USB-connected iPhone.
   - Have the user enter Apple credentials and complete device trust or developer-mode prompts locally.
   - Verify provisioning, required entitlements, and the actual signed bundle ID. If signing changes the bundle ID, reconcile the Meta app configuration before testing.
   - Do not bypass provisioning or entitlement failures. Record the exact blocker and supported remedy.
   - Record the signing expiry and renewal requirement based on the account actually used.

6. **Configure and validate**
   - Enable the current Meta companion app's required developer settings, register glasses in OpenVision, and grant the requested permissions.
   - Begin with on-demand photo questions and spoken answers. Use live vision only when needed.
   - Preserve the US$10 initial testing budget and US$25 monthly alert target. Verify what controls are available; an alert is not a hard spending cap.
   - Test launch, glasses registration, camera capture, audio output, follow-up questions, reconnect behavior, live mode, and locked-iPhone behavior.
   - Try shelf/product finding and fridge/appliance questions. Record observed results.
   - Verify compatibility for the actual glasses model, including the planned Gen 3 trial. Do not infer compatibility from the IPA building.

## Completion record

Verified 2026-10-10. No credential values appear here by design.

### Step 2 - Meta configuration: DONE
- Organization `Sudhir Morar Team`, org id `1090754000230944`, on a Managed Meta Account via work.meta.com.
  The Wearables Developer Center has no personal-Meta-account path.
- Project **Argus**, id `1414502463646819`.
- Bundle ID set to `com.sman007.openvisiontrial`. Camera and Microphone permissions enabled, each with a
  saved rationale.
- **Team ID and Universal link left deliberately blank.** That block will not save without them, which is
  expected and not a blocker: the iOS integration docs state attestation "is not used in Developer Mode",
  and a Universal link requires a domain serving an `apple-app-site-association` file. Do not invent values
  to make the block save.

### Step 3 - GitHub Actions secrets: DONE
- `OPENVISION_META_APP_ID` and `OPENVISION_META_CLIENT_TOKEN` set 2026-10-10T21:52Z. Names only recorded.
- Token format validated against the `AR|APP_ID|TOKEN` rule the workflow enforces.

### Step 4 - Build: DONE
- PR #1 merged; `main` at `4521652`, which is what put `windows-ipa.yml` on the default branch and made
  `workflow_dispatch` available.
- Run `38090314220` on `main`: **success**, 12m26s, no queue delay.
- Artifact `openvision-unsigned-iphone`, 46.5 MB, **expires 2026-10-17T22:21Z**.
- SHA-256 `8b4be594aa7f06284a738ad3e663f334d6928ee021ac3606fabd445a215057b6`, verified OK locally at
  `C:\Projects\OpenVision-ipa\`.
- IPA contents verified: `Payload/OpenVision.app`, `CFBundleIdentifier` `com.sman007.openvisiontrial`,
  `MWDAT.MetaAppID` matching the Argus project, client token prefix matching that App ID, URL scheme
  `openvision`, `MinimumOSVersion` **18.0**, and no `_CodeSignature` (correct for unsigned).
- The `.spm` cache was cold on this run and is now populated for `main`; later builds should be faster.

### Step 5 - Sign and install: DONE
- 2026-10-10 22:12 local. Sideloadly (per-user install), free Apple ID, IPA from
  `C:\Projects\OpenVision-ipa-v2\` (run `38094866350`, with the frame-pruning fix and model picker).
- Device: iPhone 17 Pro Max (`iPhone18,2`), iOS 27.0, over USB.
- **Signed bundle ID: `com.sman007.openvisiontrial.RT3XL7UTBS`** (Sideloadly appended a team suffix).
  Developer Mode does not check the bundle ID against Argus, so no Argus change was made; revisit only if
  glasses registration fails on it.
- ExternalAccessory entitlement did NOT block signing on the free account.
- **Signature expires about 2026-10-17 22:10 local** (7 days). Re-run Sideloadly with the phone plugged in
  before then. Free account App ID slots remaining: 9.
- USB gotcha: the phone charged but did not enumerate (no device, no chime) through the dock and on the
  first cables. It connected after the iPhone's Wired Accessories setting was changed and the phone was
  restarted/replugged. Windows never shows its own "allow" prompt; the Trust prompt is on the phone.

### Step 6 - Glasses and SmolVLM2: IN PROGRESS (paused 2026-10-11 ~00:15 local)
- **Registration fixed and working.** "Wearables Device Access Toolkit configuration is invalid or
  incomplete" was an empty `MWDAT.TeamID`; the build now fills it from the `OPENVISION_APPLE_TEAM_ID`
  repo variable (`RT3XL7UTBS`). Meta AI Developer Mode on; OpenVision shows Registered, 1 device.
- **Camera stream NOT yet working:** "Device session did not start (timeout)" after camera permission
  was granted. Next try: glasses on and awake, tap Install/Update Glasses App, then Start Camera Stream.
- **SmolVLM2 crash cause found:** iOS kills the app ~2.5 s after launch with
  `jetsam per-process-limit` because it preloads the 4.5 GB model. The app needs
  `com.apple.developer.kernel.increased-memory-limit`.
  - Unsigned builds embed no entitlements. Run `38108959385` now ad-hoc signs with the entitlement
    (IPA at `C:\Projects\OpenVision-ipa-v4\`, verified to contain it).
  - **Sideloadly strips it anyway** (installed app checked over USB: only the 4 basic entitlements).
    Do NOT download SmolVLM2 on a Sideloadly install; it crash-loops and only deleting the app escapes.
  - **Switching installer to AltStore Classic**, which supports this entitlement on free accounts.
    Done so far: Microsoft Store iCloud uninstalled (user approved). Apple's signed web iCloud
    installer was launched and is waiting in its window. Installers are in
    `%USERPROFILE%\Downloads\AltStoreSetup\` (AltServer unsigned, from cdn.altstore.io).
  - Next: finish iCloud install, install AltServer (run as admin), install AltStore to the phone,
    install the v4 IPA through AltStore, then verify the entitlement over USB BEFORE downloading
    SmolVLM2. Tool for that check: `pymobiledevice3 apps query <bundle id>` (venv in session scratchpad,
    reinstall with `pip install pymobiledevice3` if gone).
- The v4 Sideloadly install currently on the phone works for everything except on-device models.
- Lesson: check that a model fits the memory limit before telling the user to download it.

### Outstanding and owed
- Gemini and OpenAI tests not started.
- **Repository kept public by explicit decision**, accepting that the artifact publishes the Meta client
  token for its 7-day retention. **A token rotation is owed once testing ends**, after which the GitHub
  secret must be re-set.
- `main` does not yet contain `docs/chatgpt-glasses-setup.md`; it was pushed to the branch after the merge.

### Still to record when reached
The camera-source test from `docs/chatgpt-glasses-setup.md` section E, and real cost per task.

## Copy-and-paste desktop agent prompt

Steps 1-4 are done. This prompt resumes at step 5.

```text
Resume the OpenVision setup. Steps 1 through 4 are COMPLETE and verified:
repo, Meta project Argus, GitHub Actions secrets, and a successful build.
Do not redo them.

Local checkout: C:\Projects\OpenVision (verify its git remote; never
invent another path). Verified IPA: C:\Projects\OpenVision-ipa\.

Read OPENVISION_SETUP_PLAN_AND_PROMPT.md and its Completion record first,
then docs/chatgpt-glasses-setup.md and docs/windows-iphone-trial.md.
Both live on feature/windows-ipa-build, which is ahead of main.

CHECK FIRST, because these expire or drift:
- The artifact openvision-unsigned-iphone expires 2026-10-17T22:21Z. If it
  is gone, re-dispatch windows-ipa.yml on main rather than hunting for it.
- Whether the Meta client token has been rotated. If it has, re-set the
  GitHub secret OPENVISION_META_CLIENT_TOKEN from the new value.

Step 5 (mine to perform, not yours): sign and install the IPA with
Sideloadly from https://sideloadly.io over USB. I enter my Apple ID
password and 2FA myself. Tell me what to do and read errors back to me.
Afterwards I will tell you the bundle ID Sideloadly actually signed; if it
differs from com.sman007.openvisiontrial, reconcile the Meta Argus project
configuration to match before any glasses registration is attempted.

Step 6: Meta AI app Developer Mode (Settings > About, tap the version five
times), then OpenVision Settings > Glasses > Register. Then the acceptance
tests in docs/chatgpt-glasses-setup.md section E, starting with the camera
source test, which distinguishes glasses frames from iPhone frames.

Known constraints, already established. Do not re-derive or contradict
these without new evidence:
- A ChatGPT subscription CANNOT drive OpenAI Realtime live video. The
  subscription path talks to chatgpt.com/backend-api/wham (Responses API
  only). Live video needs an OpenAI API key, a Gemini key, or on-device
  SmolVLM2.
- Team ID and Universal link in the Meta project are deliberately blank.
  Developer Mode does not use attestation. Do not invent values.
- The app requires iOS 18.0 or newer and declares the external-accessory
  background mode. A free Apple ID may lack that entitlement; if signing
  fails there, record the exact error, do not work around it.

Keep credentials out of chat, commits, logs and screenshots. Do not put an
OpenAI key in GitHub Actions; it is configured in the app on the phone.
Preserve the US$10 initial and US$25/month alert targets, and distinguish
alerts from hard caps.

Report verified results only, and say plainly what is still unverified.
```
