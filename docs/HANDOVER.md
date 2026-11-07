# gInk / ppInk handover

Status: **blocked before feature work**. Option B was approved for baseline compatibility validation only. That gate is not passed. No product code has been changed.

Date: 2026-11-07
Owner: Chairman
Approach: Option B (ppInk as the base). Not started.

## Git

| Item | Value |
|---|---|
| Repository | https://github.com/adeyholar/gInk |
| Upstream | https://github.com/geovens/gInk |
| Branch | `docs/baseline-compat-gate` |
| Base | `master` @ `78aa853bcdc274745a42b776ca3ee22b80179f02` |
| This document commit | filled in by the follow-up commit on this branch |
| Product code | unchanged |

`adeyholar/gInk` matches upstream gInk at that base commit. It is not a ppInk fork. Option B still needs a separate fork of https://github.com/pubpub-zz/ppInk before any ppInk work. That fork was not created.

## Decision

- Proceed with Option B only as far as baseline compatibility.
- Do not roll back `Microsoft.Ink.dll`, do not enable legacy CAS (`NetFx40_LegacySecurityPolicy`), and do not change system files.
- If compatibility fails, stop and report alternatives. Do not implement features.
- Upstream MIT notices stay in place. `license.txt` was not modified.
- The Chairman merges every PR. This change is docs only.

## Findings

Microsoft documents the Ink regression fix in [KB5067036](https://support.microsoft.com/en-us/servicing/os/windows-11/2025/10/october-28-2025-kb5067036-os-builds-26200-7019-and-26100-7019-preview) (28 Oct 2025), under Input: an issue with `microsoft.ink.dll` and related APIs could stop pen and handwriting input or close apps because of thrown exceptions. That preview produces:

| Windows 11 | First documented fix build |
|---|---|
| 24H2 | 26100.7019 |
| 25H2 | 26200.7019 |

Later cumulative updates on those branches supersede that preview. Builds below those numbers, after the Aug/Sep 2025 regression (around KB5064081, build 26100.5074), are outside the documented fix. The KB does not apply to 23H2.

ppInk v1.9.100 (14 Oct 2025) is older than KB5067036 and still uses system `Microsoft.Ink`. Its readme still tells users to run `install_microsoft_ink_dll.bat` as admin until Microsoft fixes the issue. That script is out of scope.

gInk targets .NET Framework 3.5. The 24H2 `Microsoft.Ink.dll` was rebuilt for a newer runtime, so the same KB is not a reason to treat gInk as fixed. ppInk targets .NET Framework 4.8, which is why the gate is about ppInk.

## Tests actually run

| Test | Result |
|---|---|
| Read `adeyholar/gInk` tree | Pass as a fact check. Tip is upstream `78aa853`. No `docs/` tree before this branch. No local product commits. |
| Read ppInk readme (master) | Pass as a fact check. Admin DLL-rollback notice is still present. |
| Read Microsoft KB5067036 notes | Pass as a fact check. Ink.dll fix text and builds 26100.7019 / 26200.7019 confirmed. |
| `winver` on the Chairman's PC | **Not run.** OS version and build are unknown. |
| Launch official ppInk v1.9.100 | **Not run.** |
| Draw one stroke | **Not run.** |
| Acceptance row 8 (24H2+ startup, no SecurityException, no DLL rollback) | **Not run. Not passed.** |
| Build ppInk or gInk | **Not run.** This environment is Linux and has no `Microsoft.Ink`. |

No DLL was replaced. No security policy was weakened. No installer was run.

## Blockers

1. The target Windows build is unknown, so coverage of KB5067036 cannot be claimed.
2. ppInk was not executed on that PC.
3. ppInk cannot be executed in the Linux review environment.
4. There is no `adeyholar` fork of ppInk yet. This gInk fork is the wrong tree for Option B code.

## If the on-PC test fails

- Under the fix floor: install the current cumulative update through Windows Update and retest. Do not transplant an older `Microsoft.Ink.dll`.
- On or above the fix floor, and it still throws `SecurityException` or `MethodAccessException`: Option B is blocked.
- Supported alternatives then: Epic Pen Basic (free, no fork); or Option C (no `Microsoft.Ink`) only after a new explicit approval.
- Do not switch the implementation to gInk and call the KB a fix.

## Next step

Chairman, on the Windows PC:

1. Run `winver`. Record Version and OS Build.
2. Documented-fix floor: 24H2 build >= 26100.7019, or 25H2 build >= 26200.7019.
3. Download official ppInk v1.9.100 and extract it with 7-Zip (Explorer unzip has a separate startup failure, ppInk issue 357).
4. Launch it. Do not run the `.bat`. Do not add a legacy security policy.
5. Pass only if it opens and one stroke draws, with no `SecurityException` / `MethodAccessException`.

Reply with `winver` and that launch result. On pass, the next PR is a fork of pubpub-zz/ppInk, MIT notices kept, feature branch, Chairman merges. On fail, implementation stays stopped.

## Licensing

Both gInk and ppInk are MIT. Weizhi Nai's notice and ppInk's added copyright lines stay with any later copy. This document does not replace `license.txt`. `Microsoft.Ink.dll` is a Microsoft binary and is not covered by MIT. It was not redistributed here.
