# Windows + iPhone trial for Ray-Ban Meta glasses

This branch provides an **experimental unsigned IPA**. It is not a confirmed install or Gen 3 integration, and an unsigned IPA is not usable on an iPhone until signed and installed.

## Before the first build

1. Create your own Meta Wearables developer app at https://wearables.developer.meta.com/ . Enable Wearables; collect your **Meta App ID** and **full client token**, formatted `AR|APP_ID|TOKEN`.
2. In your fork open **Settings → Secrets and variables → Actions**. Add two *repository secrets*: `OPENVISION_META_APP_ID` and `OPENVISION_META_CLIENT_TOKEN`. Never put tokens in issues, chat, commits, or workflow logs.
3. On the **Actions** tab select **Windows iPhone trial IPA**, choose **Run workflow** and select this branch. The workflow compiles on a GitHub-hosted macOS runner, then packages `OpenVision-unsigned.ipa` and `SHA256SUMS.txt` as an artifact.
4. Download the artifact ZIP under the completed workflow run on your Windows PC. Extract it. Verify the SHA-256 checksum before using the IPA.
5. Try signing and installation with https://sideloadly.io/ (download ONLY from its official site). Use your own Apple ID and local USB-connected iPhone. Do **not** send Apple credentials to GitHub or to this chat. Free Apple signing typically expires after 7 days. Sideloadly may fail if entitlements or provisioning for Meta Wearables are unsupported; that outcome is a test result, not something to bypass.
6. If installation succeeds, use Meta AI on the iPhone to enable Developer Mode (per upstream SETUP.md), then open OpenVision → Settings → Glasses → Register and authorize the Meta app.
7. Test a photo request first, then live video, audio output, a locked-screen session and your appliance/fridge tests. Gen 3 compatibility and reliable locked-phone video have **not** been established.

## Troubleshooting criteria

- **Missing Meta secrets:** workflow stops before compiling.
- **GitHub build fails:** look in the Action step logs; upstream's CI is a useful compilation reference.
- **IPA cannot be signed or installed:** likely iOS provisioning / entitlements; do not interpret as glasses incompatibility.
- **App launches but glasses registration fails:** check Meta developer app registration, matching App ID/client token and URL scheme. A re-build is required when Meta credentials change.
- **Registration works but no camera:** examine the SDK hardware compatibility separately.
- **Video stops with the screen locked:** known possible iOS background restriction. Do not rely on this for mobility/navigation.

## Costs and security

GitHub public-repository standard hosted runners may be free within GitHub's current policy; review Actions usage. Do not add OpenAI keys to CI. Configure any OpenAI API key **inside the app** after installation, with a small usage budget. Meta credentials are embedded in the compiled app, as required by this integration: use an app-specific client token, not a privileged server secret. GitHub artifacts are deleted after 7 days.

## Important

Use this only as a controlled trial. The OpenVision upstream install path remains Xcode on a Mac. This workflow does **not** guarantee installation via Windows or compatibility with Gen 3 glasses.
