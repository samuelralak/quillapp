## How I would build this

**Signing (makers only)**
- NIP-07 extensions + NIP-46 remote signers so mobile/no-extension makers can sign and NIP-44-decrypt their dashboard.
- A generated local key with a passphrase-encrypted, exportable backup. Never store a raw nsec unprotected.

**Relays & delivery**
- Publish forms to the owner's NIP-65 (`10002`) write relays plus a well-known relay; carry relay hints in the `naddr`.
- Deliver responses to the owner's NIP-17 DM relays (`10050`). NIP-17 says publish *only* there; if the owner has no `10050` list they aren't set up to receive (don't fall back to arbitrary relays).
- Read gift wraps from AUTH-gated relays: NIP-59 says relays SHOULD guard `kind 1059` to the p-tagged recipient (NIP-42 is the AUTH mechanism). For a privacy product, run or rent such a relay rather than reading off open ones.

**Reading at scale**
- Tallying stays client-side (only the owner's key decrypts).
- For large N: cache decrypted rumors in IndexedDB keyed by gift-wrap id, sync incrementally (`since`/`until`), keep a local aggregate index.
- A self-hosted inbox relay (strfry/khatru) gives reliable storage without trusting public relays.

**Anti-abuse (the real work for anonymous submissions)**
- Require NIP-13 proof-of-work on the gift wrap.
- Put a capability token in the share link for unlisted forms.
- Rate-limit at the response relay.

**Lifecycle**
- `30078` is addressable: re-publish the same `d` tag to edit in place. Keep a schema `v` and never reuse a `fieldId`, or you remap past answers.
- NIP-09 (`kind 5`) to retract a form (relays may ignore it).
- For private *questions* on unlisted forms, NIP-44-encrypt the form `content` to a view key in the URL fragment (`#…?vk=…`). Soft privacy: anyone with the link can read. (A design choice, not a NIP.)

**Interop**
- For single-question polls, support NIP-88 (`1068` poll / `1018` response) so other clients can render and vote.
- For multi-question forms, cross-client support means publishing a forms NIP (or aligning with the NIP-101 draft). Until then `1070` is app-private, which is fine for a closed product.
