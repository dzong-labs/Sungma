---
title: Drop the chrono advisory ignore
type: todo
summary: Remove the RUSTSEC-2026-0543 ignore from `.cargo/audit.toml` once `sungma-bff` no longer depends on chrono, directly or through openidconnect and oauth2.
priority: low
status: open
cites:
  - "[[drop-the-rsa-advisory-ignore]]"
  - "[[author-needs-an-oauth-bff-crate]]"
---

# Drop the chrono advisory ignore

`.cargo/audit.toml` ignores RUSTSEC-2026-0543, which marks `chrono` unmaintained, so `cargo audit --deny warnings` passes in CI. The advisory is informational: it reports no flaw, covers every version and lists no patched one, so no chrono release will clear it. It recommends `jiff` or `time` instead.

`sungma-bff` depends on chrono directly, in `src/bff.rs` and `src/testkit.rs`, and through `openidconnect` 4.1 and `oauth2` 5.0. Both are their latest releases, and chrono is part of openidconnect's public API: its ID-token times are chrono `DateTime`s. Removing the direct dependency alone leaves the advisory in place.

## When to act

1. **When openidconnect drops chrono.** Update it, move `sungma-bff` to `jiff` or `std::time`, and delete the ignore.
2. **When `sungma-bff` drops openidconnect.** A hand-written OIDC client, with PKCE, nonce, code exchange and ID-token validation through `jsonwebtoken` against the issuer's JWKS, removes chrono and also clears [[drop-the-rsa-advisory-ignore]].
3. **If an advisory reports a flaw in chrono.** Then the ignore no longer covers it, and the replacement becomes urgent.

## Done when

`cargo tree -i chrono` finds nothing, `.cargo/audit.toml` no longer ignores RUSTSEC-2026-0543, and `cargo audit --deny warnings` passes.
