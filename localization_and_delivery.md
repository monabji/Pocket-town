# Pocket Town Localization and NGO Delivery

## Indian setting with adaptable contexts

The default setting is Gulmohar Ward, a fictional Hyderabad neighborhood. Kirana quantities, school commutes, a bank branch, UPI screens, local printing costs, and a community learning center make choices recognizable. These details are story context, not a claim that all Indian students have the same family structure, income, job, bank access, or language.

At setup, the learner chooses a fictional goal and a money context. The default uses a small allowance plus supervised school-fair task pay. Alternate contexts may use a stipend, family-allocated expense money, or supervised fair earnings. The ledger labels each source accurately. All costs and objectives scale together so the same concepts and safe routes remain available. A learner is never asked to enter real income or account details.

## Language-ready content

- Plan content paths for English, Telugu, and Urdu. Confirm first-release language priorities with the NGO partner.
- Keep story and UI strings in the versioned content pack, with stable keys and placeholders for amount, date, character, and goal. Never concatenate English fragments to build translated sentences.
- Give translators scene intent, speaker relationships, the expected decision, and screenshots. Review the financial meaning of words such as deposit, interest, balance, risk, and collect request with local educators.
- Format rupees, numbers, and dates consistently for the selected locale while keeping arithmetic unchanged. The player may switch language without changing game state.
- Urdu requires right-to-left layout review of dialogue, choice order, icons, number runs, ledger columns, and map labels. Do not simply mirror every visual asset.
- Support captions for audio, optional audio narration for dialogue and consequences, and an easy replay of explanations.

## Accessibility and usability

The pixelated 2.5D buildings need icon and text labels, not color alone. Keep labels and entrance markers above visual layers, and provide an accessible building list for learners who cannot navigate the spatial map. Touch targets and text must remain usable on midrange phones and with larger font settings. Screen reader order follows speaker → dialogue → choice → consequence → ledger explanation. Motion and sound can be reduced, including skipping avatar walking transitions. The player can pause after a consequence and revisit the explanation without replaying a money transaction.

Each building interaction targets a short session. The map should make the next relevant building visible without forcing a single path through all optional conversations. When a bottleneck appears, highlight the exact earlier building and explain the connection in plain language. Offer a hint and a simpler variant of the same decision without reducing the student to a failure label.

## Classroom flow

1. A facilitator loads the content pack while online if possible and creates an anonymous class code.
2. Students join on personal or shared devices using a pseudonymous learner ID. Shared-device switching must close the previous student's session cleanly.
3. The game runs offline, saves locally, and queues anonymous progress for later sync.
4. A facilitator may pause after a map for a two-minute discussion prompt about choices and tradeoffs. No student is required to reveal a private choice aloud.
5. The facilitator view shows aggregate map progress, transfer outcomes, bottlenecks needing hints, language selection, and content failures. It does not rank students by money or expose raw demerit vectors.

## Safety review before NGO deployment

Use only fictional products and scenarios. Explain that investment outcomes are assumptions, not promises. A savings-account scene teaches what to ask; eligibility and guardian requirements can vary. Fixed deposit and tax terms are illustrative and need current primary-source review before release. The app never asks for an OTP, UPI PIN, Aadhaar, real bank account, or family income. Any simulated suspicious message must lead to a safe verification path, not a real link or payment flow.

## Operational checklist

- Bundle all required scenes and authored bottlenecks for offline play.
- Test installation, storage, save/resume, and audio/text mode on the NGO's actual shared devices.
- Provide a one-page facilitator guide for starting, pausing, hints, privacy, and post-session discussion.
- Publish a content version and translation review date so facilitators know which pack a classroom used.
- Provide an export of aggregate progress and a way to delete a pseudonymous learner save.
