# Compatibility

PvZ Debugger supports known profiled official Windows releases, selected regional
builds, and two beta builds. A profile identifies an executable; it does not
promise that every feature is validated on every machine.

## Current profiled Windows builds

| Version | Edition / region | Distribution | Depth |
| --- | --- | --- | --- |
| 0.1.1.1014 | Bloom and Doom Beta EN | Standalone pre-release | Partial / extended tools |
| 0.9.9.1029 | PvZ Beta EN | Standalone pre-release | Partial / extended tools |
| 1.0.0.1051 | Original EN | PopCap standalone | Extended |
| 1.2.0.1065 | Original EN | Standalone | Basic |
| 1.0.4.7924 | Original ES | Standalone | Basic |
| 1.0.7.3556 | Original ES | Standalone | Basic |
| 1.0.7.3467 | Original RU | Standalone | Basic |
| 1.2.0.1073 | GOTY EN | Origin/EA | Basic |
| 1.2.0.1093 | GOTY DE/ES/FR/IT | Origin/EA multilingual | Basic |
| 1.2.0.1096 | GOTY EN | Steam | Extended |
| 1.1.0.1056 | GOTY JA | Regional standalone | Basic |
| 1.1.0.1056 | GOTY ZH, 2010-08-02 | Regional | Basic |
| 1.1.0.1056 | GOTY ZH, 2012-06-05 | Regional | Basic |
| 1.1.0.1056 | GOTY ZH, 2012-07-17 | Regional | Basic |

Steam may show PlantsVsZombies.exe as a launcher; the playable process is
usually popcapgame1.exe.

## Windows targets

The packaged x86 application targets Windows 7 RTM through current Windows 11,
including 32-bit Windows and WoW64. Windows 7, 8, and 8.1 are not continuously
tested and may have additional runtime or permissions problems.

## What “supported” means

The executable identity and declared structures are recognized. Feature controls
are independently gated, so a Basic profile may expose only a subset of the
tools. Unknown, repacked, translated, or patched executables may be rejected or
may behave differently from the reference build.
