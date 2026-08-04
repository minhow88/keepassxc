# KeePassXC Self-Hosting Hardening Notes

This fork includes hardening modifications for privacy-focused self-hosting deployments.

## Summary of Changes

### Privacy Hardening

| Change | Original Default | New Default | Rationale |
|--------|-----------------|-------------|-----------|
| Automatic update checks | ON (prompted) | OFF | No unsolicited outbound connections |
| Update check prompt on first run | Shown | Suppressed | Users manage updates manually |
| KPXC_FEATURE_UPDATES (CMake) | ON | OFF | Update check code excluded at compile time |
| User-Agent in update checker | Qt default (leaks Qt version) | "KeePassXC" (minimal) | Reduce fingerprinting surface |

### Security Hardening

| Change | Original Default | New Default | Rationale |
|--------|-----------------|-------------|-----------|
| Idle lock timeout | 900s (15 min) | 300s (5 min) | Faster auto-lock for shared environments |
| Lock on minimize | OFF | ON | Prevent shoulder-surfing |
| Clipboard clear timeout | 10s | 5s | Reduce exposure window for copied secrets |
| Hide notes in preview | OFF | ON | Prevent accidental data exposure |

### Robustness Hardening

| Change | File | Rationale |
|--------|------|-----------|
| 1 MB response limit on UpdateChecker | `src/networking/UpdateChecker.cpp` | Prevent memory exhaustion from malicious/hijacked responses |
| 5 MB response limit on IconDownloader | `src/gui/IconDownloader.cpp` | Prevent memory exhaustion from oversized favicon responses |
| 1 MB response limit on HibpDownloader | `src/networking/HibpDownloader.cpp` | Prevent memory exhaustion from malicious HIBP responses |

## Licensing

All changes are made under the same GPL-2/GPL-3 license as the original KeePassXC project.
No additional licensing restrictions are introduced.

## Building for Maximum Isolation

To build with all network features completely disabled at compile time:

```bash
cmake -DKPXC_FEATURE_NETWORK=OFF ..
```

This will automatically disable:
- Update checking
- Favicon downloading
- HIBP password breach checking

The application will function fully offline with no external network communication whatsoever.

## What This Fork Does NOT Change

- No telemetry or analytics are added (none existed in the original either)
- No data collection of any kind
- HIBP password checking remains user-initiated only (when network is enabled)
- Favicon downloading remains user-initiated only (when network is enabled)
- DuckDuckGo icon fallback remains OFF by default
- All cryptographic implementations remain unchanged
- Database file format remains fully compatible with upstream KeePassXC
