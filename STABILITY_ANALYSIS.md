# WARP Client Stability Analysis Report

## Executive Summary

WARP is a mature patching framework for Ragnarok Online (RO) clients. After analyzing the codebase, this report identifies client compatibility patterns, known issues, and recommendations for stable patching.

---

## Supported Client Range

Based on code analysis:

| Metric | Value |
|--------|-------|
| **Earliest supported client** | 2008-01-03 |
| **Latest supported client** | 2024+ |
| **Best tested era** | 2017-2021 |
| **Compiler versions** | VC6, VC9, VC10, VC11, VC14.16, VC14.29 |

### Version Detection

WARP uses two main properties for client detection:
- `Exe.BuildDate` - Client compilation date (YYYYMMDD format)
- `Exe.Version` - Visual C++ compiler version (6, 9, 10, 11, 14+)

---

## Client Era Compatibility

### 2020-2021 Era (Most Mentions: 197+ references)
- **Status**: Best supported
- **Reason**: Most development activity during this period
- Most patches specifically target this era

### 2018-2019 Era
- **Status**: Well supported
- **Notes**: Some patches require `Exe.BuildDate >= 20180621` or similar checks

### 2017 and Earlier
- **Status**: Partial support
- **Known Issues**:
  - `CustomFriendWin` - Not working before 2017
  - `CustomInventoryLimit` - Not working before 2017
  - `AllowSkillSpam` - Patterns missing pre-2017
  - `AllowPL2Leave` - Pattern not present before 2017-10-18
  - `FixAchieveCounters` - Different behavior post 2017-10

### 2012 and Older
- **Status**: Limited support
- **Known Issues**:
  - `BossDropIdentified` - Needs fix before 20120200
  - `HideGameWindows` - Needs fix for 2012 and older
  - `ResizePMBox` - Pattern missing in 2008 clients
  - `ResizeMobBar` - No match before 2012-04-10RE

### 2022-2024 Era (Latest)
- **Status**: Active development
- **Notes**: Recent commits focus on fixing patches for 2024 clients
- `VC14.29` compiler has specific handling in many patches

---

## Patches with Known Compatibility Issues

### Experimental/Unstable Patches (marked in YAML)

| Patch | Issue |
|-------|-------|
| Enable Emblem hover for BG | Experimental |
| Allow unknown '/command's | Experimental |
| Always load client plugins | Experimental |
| Auto mute audio | Experimental |
| Add Chris' lua overrides | Experimental |
| Custom Player/Homun/Mercenary skills | Experimental |
| Allow shortcuts in WINE | Experimental |
| Disable Doram character creation UI | Experimental |

### Patches with TODO/FIXME Comments

| Patch File | Issue Description |
|------------|-------------------|
| `CustomAura.qjs` | 2nd set of PUSHes not found in VC14.16 clients |
| `CustomAura.qjs` | Failing for very old clients |
| `CustomDLL.qjs` | Need alternative for clients with repetitive imports |
| `CustomSound.qjs` | Pre-VC11 clients failing |
| `DrawShieldOnTop.qjs` | Older and VC11 clients don't work |
| `IncrNewCharHairs.qjs` | VC14.16 has new interface |
| `ResizePlayerBar.qjs` | Pattern changed for newer clients |
| `CustomMerchantURL.qjs` | 2020-03 VC11 client has failure |
| `RemoveBookingJobs.qjs` | Some 2012 clients don't comply |

---

## Recommended Patches (marked as `recommend: yes`)

These are the most stable and well-tested patches:

### Client Category
- Enable '/who' command
- Enable '/showname' command
- Increase Camera Angles (MEDIUM)
- Restore Login Window
- Use Old Login Packet

### Data Category
- Read Data folder first
- Load custom lua file
- Custom Homunculus S lua file
- Always load Korea ExternalSettings lua
- Skip license screen
- Skip service select
- Multiple GRFs

### Environment Category
- Custom window title
- Remove serial display
- Enable multiple clients

### Network Category
- Enable proxy support
- Use custom packet keys
- Disable packet encryption

### UI Category
- Show hidden buttons
- Disable Hallucination wavy screen
- Enable party name in chat
- Show tip on right click

---

## Stability Recommendations

### For Maximum Stability

1. **Use clients from 2019-2021 era**
   - Best patch coverage
   - Most testing has been done
   - Fewest edge cases

2. **Avoid experimental patches on production servers**
   - Look for `[Experimental]` tag in patch names
   - Check TODO comments in patch scripts

3. **Use recommended patches first**
   - Patches marked `recommend: yes` are well-tested
   - Start with essential patches, add others incrementally

4. **Client versions to prefer**:
   - 2020-04-01
   - 2020-11-03 (referenced in 197 script mentions for 2020)
   - 2019-04-01 / 2019-05-30

5. **Avoid mixing very old and new patches**
   - Some patches have conditional logic that may conflict
   - Test each patch individually before combining

### For Latest Clients (2022-2024)

1. Check the recent commits in rock_win32 branch for fixes
2. Several patches were fixed in August 2024 for 2024 clients
3. `VC14.29` compiler requires special handling in many patches
4. Be prepared for some patches to fail - development is ongoing

### For Very Old Clients (pre-2015)

1. Many modern patches won't work
2. Use only patches with explicit old client support
3. Check patch `.validate` functions for build date requirements

---

## Common Failure Patterns

### Pattern 1: Compiler Version Mismatch
```javascript
if (Exe.Version === 6) // VC6 specific code
else if (Exe.Version > 10) // VC11+ specific code
```
**Impact**: Patch may fail silently or apply incorrectly

### Pattern 2: Build Date Boundaries
```javascript
if (Exe.BuildDate < 20180700) // Different patterns before this date
```
**Impact**: Pattern search fails, patch returns error

### Pattern 3: Missing Validation
Some patches lack proper `.validate` functions, causing runtime errors instead of graceful skips.

---

## Testing Recommendations

1. **Always test in isolated environment first**
2. **Apply patches one at a time initially**
3. **Keep original client backup**
4. **Check WARP console output for warnings**
5. **Join the WARP Discord for community support**

---

## Files Modified During This Analysis

- Location: `/home/user/WARP/`
- Branch: `claude/warp-stability-investigation-5RMc6`

## References

- [WARP GitHub Repository](https://github.com/Neo-Mind/WARP)
- [WARP Wiki](https://github.com/Neo-Mind/WARP/wiki)
- [WARP Discord](https://discord.gg/WGeB4wZZgS)
- [Issue Tracker](https://github.com/Neo-Mind/WARP/issues)

---

*Report generated: 2026-01-10*
*Analysis based on rock_win32 branch commit cf8d6bf*
