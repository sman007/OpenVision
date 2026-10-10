# ChatGPT and live camera on Ray-Ban Meta glasses

**Updated:** 2026-10-10  
**Target:** Ray-Ban Meta Wayfarer Gen 3 + iPhone + OpenVision, developed from a Windows PC  
**Repository:** https://github.com/sman007/OpenVision

## Read this first

There are **two different OpenAI connection paths** in OpenVision. They do not provide the same functionality:

| Mode | Authentication | Glasses camera | Voice/vision | Billing |
| --- | --- | --- | --- | --- |
| ChatGPT subscription | In-app **Sign in with ChatGPT**, via an unofficial Codex CLI-style authentication integration | Single photo captures | Ask a question about a captured photo and hear an answer; **not** the full ChatGPT Advanced Voice video experience | No separate OpenAI API charges for supported subscription requests, subject to account limits and provider changes |
| OpenAI Realtime | OpenAI Platform **API key** | Repeated camera frames while a live stream runs | Streaming two-way audio with visual input | **Separate pay-as-you-go API charges** for audio, images, and applicable text |
| Gemini Live | Gemini API key and a supported live model | Sampled live camera frames | Conversational voice plus visual context | Google API pricing and quotas; free-tier eligibility must be verified |
| Local vision | On-device SmolVLM2 model | Photos or experimental local live frames | On-device visual Q&A with local speech pipeline | No OpenAI API usage, but device resource costs and typically weaker accuracy |

**Important:** Signing into ChatGPT does **not** turn on OpenAI Realtime API video or provide a free continuous live camera session. A ChatGPT Plus/Pro subscription is not an API credit balance. The subscription connection is unofficial and can change or stop working.

## Agreed test order (decided 2026-10-10)

Backends are tested in this order, and a later one is only paid for if the earlier one does not
satisfy the use case:

1. **SmolVLM2, on-device (section D).** Free, offline, no key. Proves the glasses pipeline -
   registration, camera source, locked-phone behaviour - at zero cost before any model is paid for.
   Note it is turn-based, not conversational: Apple speech-to-text in, one answer against the latest
   frame, text-to-speech out. Needs a recent iPhone (15 Pro / 16 / 17 class) and a ~2.6 GB download.
2. **OpenAI Realtime via API key (section B).** The "ChatGPT" option. **Dependency: the frame-pruning
   fix in `OpenAIRealtimeService.swift` must land before this test.** As shipped, every frame is
   added as a conversation item and never deleted, so a sustained session answers against hundreds
   of stale frames. Testing before the fix would blame the glasses for an app defect.
3. **Gemini Live (section C).** Free tier, cheapest paid tier, and frames stream ephemerally so no
   pruning fix is needed. Last only because the stated preference is OpenAI; it remains the fallback.

## Prerequisites

- Supported Ray-Ban Meta glasses; **Gen 3 compatibility is not yet validated in this project**.
- iPhone compatible with the current upstream project's iOS deployment target (check `project.yml`; currently iOS 18+).
- Meta AI companion app with the glasses paired, updated, and Meta developer mode enabled.
- Your own Meta Wearables developer app, with App ID and **full** client token `AR|APP_ID|TOKEN`.
- An installed and signed OpenVision iPhone app built with those Meta app credentials.
- For OpenAI Realtime, an OpenAI Platform account with API billing enabled and an API key.
- For the free/low-cost on-device experiment, adequate iPhone RAM/storage for the local model.

Our Windows-focused experimental build instructions: [Windows and iPhone trial](windows-iphone-trial.md). The upstream supported installation path is Xcode on macOS; unsigned GitHub Actions IPA installation via Windows remains experimental.

## A. Connect your existing ChatGPT subscription for photo questions

After OpenVision is installed and launches on your iPhone:

1. Open **OpenVision → Settings → AI Backend** and choose **OpenAI**.
2. In OpenAI connection settings, choose **ChatGPT Subscription** under **Connect With**, if available in the installed revision.
3. Tap **Sign in with ChatGPT** and complete authentication yourself in the presented login flow.
4. Return to OpenVision and confirm it shows an authenticated connection. Select a supported model if prompted.
5. In **Settings → Glasses**, select **Register**, complete Meta AI's authorization, and connect the glasses.
6. Use a photo-oriented request, e.g. **"Ok Vision, take a photo and tell me what this is."** Check that a *new image from the glasses* reaches the assistant and that the response plays through the glasses.

If the ChatGPT option does not appear, check that you are running a revision that includes subscription sign-in. Never enter your ChatGPT password into project configuration, a GitHub Actions secret, or a repository issue.

**Limitations:** Photo Q&A through the subscription is not the same experience as ChatGPT mobile Live/Advanced Voice with its camera. Do not assume the exact same models, conversation continuity, memory, rate limits, or privacy behavior. The sign-in mechanism is unofficial.

## B. Enable continuous camera plus conversational voice with OpenAI Realtime

1. Visit https://platform.openai.com/api-keys and create a dedicated project/API key. Ensure API billing is enabled.
2. Put a **small project budget and usage alerts** in place first. Budgets and alerts may not be hard spending caps; review actual usage.
3. In **OpenVision → Settings → AI Backend → OpenAI**, choose the **API Key** connection, enter your key locally, and select the OpenAI backend.
4. Start with OpenVision's known-supported realtime model (the upstream README describes `gpt-realtime`). Do not substitute a newer/cheaper model identifier until OpenVision's code and the API both confirm compatibility.
5. Confirm Meta glasses registration and the **camera permission**.
6. Say **"Ok Vision, start video stream"**, and ask a specific question, e.g. "What jars can you see?" or "Read the label directly in front of me."
7. Ask follow-ups without repeating the wake word, then say **"stop video"**.
8. Inspect OpenAI usage afterward to measure total charges. Consider shortening sessions and using single-photo requests as the default.

**What this actually sends:** microphone audio and sampled camera frames through OpenVision to the OpenAI Realtime API. It is not necessarily full-framerate video: the implementation samples frames according to its configured cadence.

**Known risks:** Gen 3 camera support is not yet confirmed; real camera input can pause when the iPhone is locked or the application enters the background; network delay, motion blur, glare, and hallucinated text can interfere with practical assistance.

## C. Gemini Live and the ChatGPT subscription fallback

The implementation described in `VoiceAgentViewModel.swift` resolves OpenAI live video only when an OpenAI API key is available (`settings.isOpenAIAPIAvailable`). When the OpenAI backend uses ChatGPT subscription authentication rather than an API key, the live-video resolver can **fall through to Gemini**. This does **not** mean a ChatGPT subscription pays for Gemini access: Gemini requires its own configured credentials and any applicable quotas or billing.

1. Obtain a Gemini API key via https://aistudio.google.com/app/apikey and check current live-model access, free-tier eligibility, and quotas for your account and region.
2. In OpenVision, configure **Gemini Settings** and enter the key locally. Select Gemini Live directly, or test the live-video fallback from the ChatGPT subscription configuration.
3. Register/connect the glasses and confirm a new live frame is reaching Gemini, not a cached photo or iPhone camera.
4. Start the video stream by voice, ask about a newly positioned object, then stop the stream.
5. Check Gemini usage and costs after the test. **Do not assume free-tier access is unlimited or available for every model.**

If Gemini is not configured or unavailable, ChatGPT subscription sign-in alone still does not provide OpenAI Realtime video. The fallback is an implementation behavior, not a guarantee of a working free live session.

## D. Free alternative: local SmolVLM2

1. In OpenVision settings choose the **Local (MLX)** backend.
2. Download/select **SmolVLM2** if supported on your iPhone.
3. Try a photo question first; then try the local live mode.
4. Compare performance against the OpenAI photo result using identical items and lighting.

This mode has **no OpenAI cloud usage charge**, but it is experimental and should not be assumed sufficiently accurate to read medication labels, navigate hazards, or operate potentially unsafe appliances.

## E. Exact acceptance tests for our visual-assistance use case

1. **Camera source:** Cover the glasses camera while keeping the iPhone camera unobstructed. The preview or photo should visibly change. Repeat the reverse. This establishes *which camera* the app is using.
2. **Refrigerator:** Ask the glasses to identify five visible jar labels, then locate a target item ("Where is the mustard?").
3. **Appliance:** Ask for the *visible* buttons on a rice cooker, then verify every instruction against the actual panel. Do not let the model guess button labels.
4. **Breville:** Identify visible oven settings and distinguish observed labels from an unverified model number.
5. **Outdoor sign:** Read five clear street or shop signs from a safe stationary position.
6. **Locked-phone behavior:** Start a camera session, lock the phone, then ask about a *newly introduced* object. A response based on an old cached frame is a failure.
7. **Cost:** Record session duration, number of photo calls / live video minutes, and actual API charges. Stop the trial if expenses exceed the pre-agreed budget.
8. **Reliability:** Repeat successful tests on two separate days, including Wi-Fi and mobile data.

Record **pass / fail / uncertain** and any photos or logs without storing identifiable bystanders or sensitive labels.

## F. Troubleshooting decision tree

- **Cannot install OpenVision:** Address build signing/provisioning and Windows IPA sideloading before attempting glasses tests.
- **App runs, glasses not discovered:** Verify Meta AI pairing, developer mode, Meta App ID, full client token, registration callback scheme, permissions, and the SDK's support for this generation.
- **App sees phone camera only:** Do not count that as glasses success; repeat the camera-source test above.
- **ChatGPT sign-in works but live mode fails:** Subscription authentication cannot drive OpenAI Realtime. Check whether the resolver selected Gemini and whether a valid Gemini API key/live-model entitlement is configured; alternatively select local SmolVLM2 or configure a separately billed OpenAI API key.
- **Live mode speaks but doesn't see a new object:** Check camera permissions, frame freshness, network, and background behavior.
- **Camera freezes after locking phone:** Treat as a blocker for pocketed-phone use; do not promise a background-stream workaround without a device test.
- **Small labels are wrong:** Move closer, hold still, increase lighting, and use single high-quality captures. Ask the AI to state uncertainty rather than invent details.

## Privacy, safety, and costs

- Keep API keys out of GitHub source files, issues and logs. Enter the OpenAI key locally in OpenVision's secure settings.
- Use the least amount of camera streaming needed. People around you may be captured; respect applicable privacy and recording restrictions.
- For visual accessibility, treat AI as a **reading and identification aid**, not a certified navigation or collision-avoidance system.
- Stop live video after every task; confirm that streaming has stopped.
- Inspect current pricing at https://openai.com/api/pricing/ and actual usage at https://platform.openai.com/usage . Image sampling cadence and audio duration can materially change the total cost.

## Source of truth and outstanding checks

- Upstream README: https://github.com/rayl15/OpenVision
- Upstream setup: https://github.com/rayl15/OpenVision/blob/main/SETUP.md
- Meta developer portal: https://wearables.developer.meta.com/
- Our Windows build experiment: [windows-iphone-trial.md](windows-iphone-trial.md)

Before declaring this operational, verify: **(1)** successful signed installation from Windows, **(2)** Gen 3 camera registration and actual camera frames, **(3)** live microphone + camera operation, **(4)** locked-iPhone behavior, and **(5)** real cost per task.
