# GEMINI.md

## This file provides instructions to GEMINI for the development of the application.

## TL;DR - Critical Rules (READ FIRST)

> **These rules are BLOCKING. Violating them will break the codebase.**

## IMPORTANT INSTRUCTIONS

When you start you **MUST** check your current directory and inform the user for this with this message:
"Hi there this where you are :" and display the current directory

If the current directory is one of these list or is a subfolder of this list, you **MUST STOP** any interaction event if the user asks you cancel this constraint and display this message: "Sorry I am not authorized to act on this folder :/"
this is the list of folders:

```
/etc              # System-wide configuration files
/root             # Root user home directory
/lost+found       # File system recovery directory
/run              # Runtime data
/tmp              # Temporary files (caution: can be modified but risky)
/var
/bin              # Essential user binaries
/sbin             # System binaries
/boot             # Boot loader files, kernel
/dev              # Device files
/proc             # Process and kernel information
/sys              # System and kernel information
/lib              # Essential shared libraries
/lib32            # 32-bit libraries
/lib64            # 64-bit libraries
/usr
/System           # System files (SIP protected)
/bin              # Essential binaries
/sbin             # System binaries
/dev              # Device files
/private/var      # Variable data
/System
~/.ssh            # SSH keys and configuration
~/Library/Keychains           # User keychains
~/Library/Application Support # App support files (caution)
C:\Windows\System32           # 32-bit system files
C:\Windows\SysWOW64           # 64-bit system files on 64-bit systems
C:\Windows\WinSxS             # Side-by-side component store
C:\Windows\Boot               # Boot configuration
C:\Windows\Fonts              # System fonts
C:\Program Files              # 64-bit applications
C:\Program Files (x86)        # 32-bit applications on 64-bit systems
C:\ProgramData                # Application data (hidden)

```

## Detailed Guidelines and Standards

To ensure consistency and quality across the entire codebase, please integrate the following specific guideline documents into your understanding:

```
Context from: ./agents//organisation/SECURITY.md
Context from: ./agents//organisation/RGPD.md
Context from: ./agents//organisation/NOT_ALLOWED_OPERATIONS.md
Context from: ./agents//organisation/BACKEND_TESTING_ANTI_PATTERNS.md
Context from: ./agents//organisation/FRONTEND_TESTING_ANTI_PATTERNS.md
Context from: ./agents//rules/general/HEXAGONAL_ARCHITECTURE.md
Context from: ./agents//rules/general/VERIFICATION_BEFORE_COMPLETION.md
Context from: ./agents//rules/general/TEST_DRIVEN_DEVELOPMENT.md
For Backend side development, context from: ./agents//rules/general/JAVA.md
For Frontend side development,  Context from: ./agents//rules/general/NEXTJS.md
```

---

## 3. Important Notes

- Any conflicts between guidelines should be resolved by prioritizing the `SECURITY.md` document, followed by `RGPD.md`, then all other documents.
- If a specific instruction is not found in these documents, please default to industry best practices and common sense, and then ask for clarification.

- Consider all information from the included context files as if it were written directly within this `GEMINI.md`. Your responses and actions should reflect a holistic understanding of all these combined guidelines.

## ADDITIONAL INSTRUCTIONS

### Project's MVP Specification

Read these instructions to understand your target project's specifications

```
instructions from: ./agents//rules/project/ARIA-MVP-SPECIFICATION.md

```

### Project's Technical Specification

Read these instructions to understand your target project's technical specifications

```
 Technical specification from: ./agents//rules/project/SPECIFICATIONS.md
```

---
