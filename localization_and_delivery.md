# Pocket Town English Accessibility and NGO Delivery

The implementation target is Godot 4.x standard GDScript with the Compatibility renderer. The NGO baseline is an installed Android APK; Windows exports support classroom computers. Browser delivery is optional and must pass its own installation, caching, storage, and input checks.

## Indian setting with adaptable contexts

The default setting is Gulmohar Ward, a fictional Hyderabad neighborhood. Kirana quantities, school commutes, a bank branch, UPI screens, local printing costs, and a community learning center make choices recognizable. These details are story context, not a claim that all Indian students have the same family structure, income, job, or bank access.

At setup, the learner chooses a fictional goal and a money context. The default uses a small allowance plus supervised school-fair task pay. Alternate contexts may use a stipend, family-allocated expense money, or supervised fair earnings. The ledger labels each source accurately. All costs and objectives scale together so the same concepts and safe routes remain available. A learner is never asked to enter real income or account details.

## English-only content

- Use English only for dialogue, choices, UI, signs, captions, facilitator materials, and optional narration.
- Keep complete English story and UI strings in the versioned content pack, with stable keys and placeholders for amount, date, character, and goal. Separate text from game logic and avoid concatenating sentence fragments.
- Give content reviewers scene intent, speaker relationships, the expected decision, and screenshots. Review plain-English explanations of deposit, interest, balance, risk, and collect request with local educators.
- Format Indian rupees, numbers, and dates consistently using English labels and Indian number grouping where appropriate. Keep arithmetic and integer-paise storage unchanged.
- Test readable English typography, full choice labels, rupee glyphs, ledger columns, and map labels at larger text sizes.
- Support English captions, optional English narration for dialogue and consequences, and an easy replay of explanations.

## Accessibility and usability

The pixelated 2.5D buildings need icon and text labels, not color alone. Keep labels and entrance markers above visual layers, and provide a keyboard-focusable building list for learners who cannot navigate the spatial map. Godot `Control` containers, themes, focus neighbors, explicit large-text settings, and generous buttons must remain usable on midrange phones. Verify screen-reader availability on the pinned Godot version and each export platform before advertising support; when supported, target speaker → dialogue → choice → consequence → ledger explanation order. Provide optional English narration, readable history, text descriptions, and a facilitator route. Motion and sound can be reduced, including skipping avatar walking transitions. The player can pause after a consequence and revisit the explanation without replaying a money transaction.

Desktop movement uses W/A/S/D or arrows; E opens a nearby building. Mobile directional buttons reuse Godot `InputMap` actions, while direct building taps and the building list avoid movement precision requirements. Visual novel mode locks movement and gives focus to readable dialogue and choices. Enter/Space advances, 1/2/3 selects visible choices, Escape pauses/closes dismissible panels, and J opens the journal. Clear held actions on focus loss, chapter entry, and learner switch.

Each building interaction targets a short session. The map should make the next relevant building visible without forcing a single path through all optional conversations. When a bottleneck appears, highlight the exact earlier building and explain the connection in plain language. Offer a hint and a simpler variant of the same decision without reducing the student to a failure label.

## Classroom flow

1. A facilitator installs a Godot Android APK or Windows export with bundled content before the session. Once class services are implemented, the facilitator creates an anonymous class code while online; local play does not require that service.
2. Students join on personal or shared devices using a pseudonymous learner ID. Shared-device switching must close the previous student's session cleanly.
3. Installed builds run bundled chapters offline and save each pseudonymous learner under `user://` through the versioned save repository. Once sync is implemented, anonymous progress is queued for later delivery. Switching learners clears the active scene, input state, and previous learner's UI.
4. A facilitator may pause after a map for a two-minute discussion prompt about choices and tradeoffs. No student is required to reveal a private choice aloud.
5. The facilitator view shows aggregate map progress, transfer outcomes, bottlenecks needing hints, accessibility support needs, and content failures. It does not rank students by money or expose raw demerit vectors.

## Safety review before NGO deployment

Use only fictional products and scenarios. Explain that investment outcomes are assumptions, not promises. A savings-account scene teaches what to ask; eligibility and guardian requirements can vary. Fixed deposit and tax terms are illustrative and need current primary-source review before release. The app never asks for an OTP, UPI PIN, Aadhaar, real bank account, or family income. Any simulated suspicious message must lead to a safe verification path, not a real link or payment flow.

## Operational checklist

- Bundle all required scenes and authored bottlenecks for offline play.
- Test installation, storage, save/resume, and audio/text mode on the NGO's actual shared devices.
- Provide a one-page facilitator guide for starting, pausing, hints, privacy, and post-session discussion.
- Publish a content version and English content review date so facilitators know which pack a classroom used.
- Provide an export of aggregate progress and a way to delete a pseudonymous learner save.

## Godot export and offline delivery

Pin the Godot 4.x editor and matching export templates. Keep the client in standard GDScript and the Compatibility renderer. Build and test the Android APK on actual NGO phones and the Windows export on classroom computers; an editor preview alone does not establish installation readiness. Keep future source under `game/{scenes,scripts,resources,content,assets,tests}` and transport schemas under `contracts/`; optional AI/sync services live separately under `services/`.

For optional Web export, use single-threaded Compatibility output and serve the generated file set over localhost for development or HTTPS for deployment. Use a server with the correct WebAssembly MIME type and all required exported files. Threaded exports require additional cross-origin isolation headers and a separate compatibility assessment. Browser loading is not equivalent to installed offline delivery. These constraints follow the [Godot Web export documentation](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html).

Enable and test Godot's PWA/service-worker export options before offering browser offline use. It requires a successful initial visit and completed asset caching; caches can be evicted. A first offline visit cannot fetch an uncached game. Browser `user://` persistence depends on IndexedDB permissions and browser behavior; private browsing, storage eviction, and embedded-page restrictions can prevent durable saves. Validate close/reopen and interrupted writes on the actual classroom browser, show an honest persistence warning when needed, and offer a save export when supported. Native installed builds remain the preferred predictable classroom path.

## Preview before phase completion

Every phase in [phases.md](phases.md) requires an actual running Godot preview and recorded evidence. Phase 0 demonstrates the starter map and home scene; Phases 1–7 add their next full level and repeat earlier-level regression. Show both keyboard and touch/direct building interaction, readable English visual novels, money outcomes, movement locking, and save/resume. Add installed-device and optional Web/offline checks when their delivery systems are introduced. Store the future evidence with build/commit, content version, target device, routes, observed results, and resolved defects; do not mark a phase complete on documentation alone.
