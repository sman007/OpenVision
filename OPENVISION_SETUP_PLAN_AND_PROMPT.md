# OpenVision setup plan and desktop continuation prompt

Updated: 2026-10-10

Repository: https://github.com/sman007/OpenVision
Setup PR: https://github.com/sman007/OpenVision/pull/1
Setup branch: `feature/windows-ipa-build`

## Verified handoff state

PR #1 is open and draft. It adds a manually triggered macOS GitHub Actions build, unsigned IPA packaging, and Windows installation instructions. A successful build, Windows installation, glasses compatibility, and locked-iPhone operation have not been verified. Recheck this state when resuming.

The user has a Windows desktop and an iPhone, with no local Mac/Xcode environment. The local C: checkout path is unknown. Do not invent a local path or assume a cloud workspace is the user's desktop.

The user authorizes completing Meta configuration, adding the required repository secrets, running and fixing the build, and progressing toward installation. Desktop browser permission has been granted, but an actual desktop browser connection must be verified.

## Execution plan

1. **Verify desktop access and repository state**
   - Confirm the browser tool controls the user's actual desktop browser.
   - If unavailable, identify the environment's supported connector and provide verified setup instructions. Do not claim that opening a conversation on desktop automatically grants control.
   - Inspect repository instructions, PR #1, current workflow, project configuration, and setup documentation.
   - If filesystem access is available, locate any existing checkout by checking its Git remote against sman007/OpenVision. Record the actual path without guessing.

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

## Completion evidence

Record the Meta configuration status without credential values, secret names, final commit/branch, successful Actions run URL, artifact/checksum verification, installation result, signed bundle ID, and device test results. Clearly distinguish pending work from verified success.

## Copy-and-paste desktop agent prompt

```text
Continue setting up OpenVision in my GitHub repository:
https://github.com/sman007/OpenVision

Read OPENVISION_SETUP_PLAN_AND_PROMPT.md on feature/windows-ipa-build
and inspect PR #1:
https://github.com/sman007/OpenVision/pull/1

Use my connected desktop browser. First verify that you can see and
control my actual desktop browser. I authorize desktop browser access.
If the connection is unavailable, explain exactly which connector this
environment supports and give verified instructions to enable it.
Do not assume opening this conversation on desktop grants access, and
do not substitute a cloud browser without telling me.

I authorize you to inspect the repo and PR, complete the Meta Wearables
developer configuration, add encrypted GitHub Actions repository secrets
OPENVISION_META_APP_ID and OPENVISION_META_CLIENT_TOKEN, review and
resolve the workflow's default-branch prerequisite, run the macOS iPhone
build, fix failures, obtain and verify the unsigned IPA, and progress
through signing and installation with Sideloadly on Windows.

Verify the App ID, full AR|APP_ID|TOKEN format, bundle identifier, and
callback URL scheme against the actual project. Check the final signed
bundle ID after sideloading and reconcile it with Meta configuration.

Keep credentials out of chat, source code, logs, and shared screenshots.
Let me enter passwords and complete two-factor authentication, account
agreements, and physical iPhone prompts. Use my existing accounts.
Do not store an OpenAI key in GitHub Actions.

If you can access my desktop filesystem, locate any existing local
checkout and verify its Git remote. Never invent a C: drive path.

Use on-demand vision first and live mode only when needed. Preserve the
US$10 initial testing budget and US$25 monthly alert target; verify the
available controls and distinguish alerts from hard caps.

Continue until installation and basic testing succeed or identify a
concrete blocker. Verify actual glasses compatibility, camera/audio,
follow-ups, reconnects, and locked-iPhone behavior. Report run links,
installation status, and observed results without exposing secrets.
```
