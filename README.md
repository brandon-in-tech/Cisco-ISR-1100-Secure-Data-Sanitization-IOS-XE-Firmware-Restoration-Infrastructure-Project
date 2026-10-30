<div align="center">
  <img src="assets/brandons-logo.png" alt="Brandon Stevenson — IT Professional" width="180">Cisco ISR 1100 — Secure Data Sanitization & IOS XE Firmware Restoration
Physical Cisco C1111-8PWB | Cisco IOS XE 16.09.05 | ROMMON | USB Firmware Recovery | Bootflash | Technical Diagnostics
</div>Evidence: 19 redacted screenshots captured from the router console.
Documentation status: Screenshot-based technical case study; not a certification of complete sanitization or successful final reimaging.

This project documents a hands-on Cisco C1111-8PWB integrated services router maintenance session. It covers ROMMON operations, platform identification, interface and routing diagnostics, cryptographic key handling, configuration and bootflash maintenance, and an attempted USB-to-bootflash firmware restoration. Each numbered step below displays the actual console screenshot inline and explains what the evidence does—and does not—establish.
Publication note: Publish console evidence only if authorized. Redaction alone does not establish permission to share underlying work. Some commands shown are destructive; this case study describes captured actions rather than instructing changes to unrelated devices.

<a id="table-of-contents"></a>
Table of Contents
1. Project Overview
2. Hardware And Environment
3. Objectives And Skills Demonstrated
4. Command Prompt Reference
5. Workflow At A Glance
6. Screenshot-By-Screenshot Walkthrough
   - Step 01 — Access ROMMON and adjust the configuration register
   - Step 02 — Identify the IOS XE software release
   - Step 03 — Inventory the router chassis and installed components
   - Step 04 — Inspect platform slots and the embedded wireless module
   - Step 05 — Establish an interface and addressing baseline
   - Step 06 — Review Ethernet interface characteristics and counters
   - Step 07 — Inspect IPv4 routing state
   - Step 08 — Review the active running configuration
   - Step 09 — Enumerate accessible file systems
   - Step 10 — Preserve the IOS XE image to USB storage
   - Step 11 — Inspect and remove applicable cryptographic keys
   - Step 12 — Review VTP-domain handling and normal boot behavior
   - Step 13 — Remove saved configuration from NVRAM
   - Step 14 — Inspect and format bootflash under an approved recovery plan
   - Step 15 — Restore the IOS XE image from USB
   - Step 16 — Inspect post-operation storage listings
   - Step 17 — Check VLAN state after maintenance
   - Step 18 — Recheck platform modules and request a restart
   - Step 19 — Inspect ROMMON environment variables
7. Evidence And Verification Matrix
8. Technical Concepts And Terminology
9. Troubleshooting Lessons And Common Pitfalls
10. Limits Of The Available Evidence
11. Privacy, Safety, And Responsible Publication
12. Repository Layout
13. Portfolio Summary
<a id="project-overview"></a>
1. Project Overview
Technical focus. This case study follows an offline Cisco router maintenance session captured through a terminal connection. It combines basic network operations (show version, inventory, interfaces, routing), firmware-management observations (dir, copy), security-related maintenance (crypto key zeroize), configuration-storage operations, and post-operation inspection.

Why this matters. Retired or re-provisioned network hardware may contain topology information, routing policy, interface addressing, cached credentials, keys, device certificates, logs, or customer-specific configuration. Responsible infrastructure lifecycle work requires identifying what is present, maintaining a recovery path, handling storage using an authorized process, and validating the resulting operational and data state. The evidence here demonstrates portions of that workflow and makes visible the boundaries of what can be concluded.

Observed versus confirmed: The screenshot set demonstrates that particular commands were entered and that some erase/format operations returned completion messages. The screenshots do not show a completed USB backup/restore message, an independent embedded-AP reset, or a successful final boot of the restored image. This README deliberately describes those outcomes as not fully evidenced.

↑ Back to Table of Contents

<a id="hardware-and-environment"></a>
2. Hardware And Environment
Category	Observed or applicable detail
Router	Cisco C1111-8PWB (ISR 1100 family)
Software release	IOS XE 16.09.05 displayed in the screenshot
Router image named in console	c1100-universalk9_ias.16.09.05.SPA.bin
Access method	Terminal/serial console, including ROMMON and IOS XE modes
Firmware storage	bootflash: (router) and usb0: (detected external storage)
Embedded hardware	ISR-AP1100AC-B wireless module visible in platform output
Network connection	No internet/uplink connection required for the documented console inspection
Evidence directory	[`evidence/`](evidence/) containing step1.png through step19.png
Evidence handling	Screenshots include opaque masking of observed serial/MAC/customer-specific values; publication authorization remains separate


Hardware caveat: This README describes one observed Cisco C1111 session. Commands for unrelated ISR generations, IOS releases, and embedded modules may differ. Before performing any live maintenance, identify the actual model, software, storage, supported capabilities, and approved organization procedure.

↑ Back to Table of Contents

<a id="objectives-and-skills-demonstrated"></a>
3. Objectives And Skills Demonstrated
1. Console and ROMMON fundamentals: Recognize low-level boot monitor access and configuration-register behavior.
2. Asset and platform discovery: Identify Cisco IOS XE release, chassis PID, installed modules, and subsystem states.
3. Layer 1–3 diagnostics: Interpret interface status, duplex/MTU/counters, and IPv4 routing-table output.
4. Configuration awareness: Distinguish running-config from startup-config and understand how recovery boots affect them.
5. Firmware management: Locate an IOS XE .bin image and understand USB copy directions, prompts, and verification checkpoints.
6. Security lifecycle operations: Explain cryptographic key zeroization and the difference between configuration erasure and complete data sanitization.
7. Post-maintenance verification: Compare observed storage, VLAN, platform, and ROMMON states without overclaiming results.
8. Technical documentation: Provide redacted evidence, command interpretation, and honest limitations for an audit-friendly case study.
↑ Back to Table of Contents

<a id="command-prompt-reference"></a>
4. Command Prompt Reference
The CLI prompt indicates where a command is entered; the prompt itself is not typed.
Prompt	Environment	Typical use in this case study
rommon 1 >	ROM monitor	Configuration register and boot environment
Router>	User EXEC	Limited monitoring/access
Router#	Privileged EXEC	show commands, storage operations, reload
Router(config)#	Global configuration	Device-wide configuration statements and key operations
Router(config-if)#	Interface configuration	Per-interface settings (not required in the captured steps)
Source filename []?	Interactive CLI prompt	Router asking for a value during a copy operation


Reading code samples: The screenshots may display different command counter numbers or interactive prompts. The code blocks below reproduce the command intent and observed prompts; angle-bracket placeholders do not appear in the images as literal file names.

↑ Back to Table of Contents

<a id="workflow-at-a-glance"></a>
5. Workflow At A Glance
Phase	Evidence steps	Engineering purpose
Boot monitor access	01	Inspect low-level boot controls
Platform discovery	02–04	Identify software and installed subsystems
Network baseline	05–08	Examine interface, routing, and active configuration state
Storage and firmware preservation	09–10	Find the IOS image and observe copying to USB
Security and storage maintenance	11–14	Observe key, configuration, and storage operations
Firmware restoration and review	15–19	Observe attempted image return and post-action inspection


This table summarizes the recorded sequence, not a recommended generic order for a different Cisco model. The evidence is numbered to match your filenames; each actual screenshot is shown inline beneath its corresponding step.

↑ Back to Table of Contents

<a id="screenshot-by-screenshot-walkthrough"></a>
6. Screenshot-By-Screenshot Walkthrough
Each step includes the observed Cisco mode, an inline console screenshot, commands, technical purpose, interpretation, and verification limits. The included images have redacted identifying values.

↑ Back to Table of Contents
<a id="step-01"></a>
Step 01 — Access ROMMON and adjust the configuration register
Phase: Boot monitor / access
CLI mode: ROMMON
Terminal evidence — Step 01

Objective. Establish a recovery boot path that does not automatically load the previously saved startup configuration. This is an access and boot-behavior change, not a data-erasure action.

Commands visible in or associated with this capture
rommon 1 > confreg 0x2142
rommon 2 > reset
Technical explanation. confreg 0x2142 changes the configuration-register setting so the normal startup configuration is ignored during the subsequent boot. It is commonly used when access to the stored configuration must be bypassed in an authorized recovery workflow. reset restarts the platform from the ROM monitor. ROMMON runs below the normal IOS XE command environment, so commands and prompts differ from those used once IOS starts.

On a Cisco C1111, boot monitor capabilities and prompts can vary by ROMMON release. The literal prompt number (rommon 1 >, rommon 2 >) is a command counter, not a required number you type.`

What this screenshot establishes. The screenshot shows the ROMMON commands and boot activity identifying the C1111 platform. It does not by itself prove that IOS XE finished booting.

Verification checkpoint. After the system reaches IOS XE, use show version to confirm the booted release. Later restore an appropriate normal boot-register value; do not leave a recovery-bypass setting unintentionally active.

Operational caution. A bypassed startup configuration may remain stored until separately removed. Do not claim sanitization from this step.

↑ Back to Table of Contents
<a id="step-02"></a>
Step 02 — Identify the IOS XE software release
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 02

Objective. Identify the operating system and establish a software baseline before assessing commands, storage, and feature support.

Commands visible in or associated with this capture
Router# terminal length 0
Router# show version
Technical explanation. terminal length 0 disables page-at-a-time output for the current terminal session, making it easier to capture complete diagnostic commands. show version reports platform and software details including IOS XE release information, the system image, uptime, and boot history.

The observed device is a Cisco C1111-8PWB, within the Cisco ISR 1100 family, and the visible release is IOS XE 16.09.05. This is important because command availability varies between IOS, IOS XE, ROMMON versions, licenses, and configured modes.`

What this screenshot establishes. The visible console output identifies the Cisco C1100 software family and the IOS XE release. It is a platform baseline, not an independent software-integrity check.

Verification checkpoint. Record the model and release only; redact board IDs, serials, and other persistent identifiers before publishing output.

Operational caution. Release identification is not evidence that a particular license or optional feature is enabled.

↑ Back to Table of Contents
<a id="step-03"></a>
Step 03 — Inventory the router chassis and installed components
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 03

Objective. Enumerate the physical hardware before determining which components may hold persistent configuration or require separate handling.

Commands visible in or associated with this capture
Router# show inventory
Technical explanation. show inventory enumerates hardware records, generally including PID (product identifier), VID (version identifier), SN (serial number), and descriptive names for modules and components. The PID helps establish that the device is a C1111-8PWB; individual module entries indicate what else may be installed.

Inventory matters for sanitization because a chassis can contain more than one storage-bearing subsystem. A router's main IOS XE environment is not necessarily the only component that may retain configuration.`

What this screenshot establishes. The screenshot displays chassis and module entries. Unique serial numbers were masked in the supplied redacted image.

Verification checkpoint. Confirm the chassis PID and compare any module list with the platform information in the following step.

Operational caution. Never post unmasked SN fields, processor board IDs, hardware asset tags, or warranty identifiers.

↑ Back to Table of Contents
<a id="step-04"></a>
Step 04 — Inspect platform slots and the embedded wireless module
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 04

Objective. Determine which route, switch, and wireless components are physically present and how the system reports their operating states.

Commands visible in or associated with this capture
Router# show platform
Technical explanation. show platform provides a view of the C1111's installed slots and system elements, with state information such as ok or active. In this evidence, the output includes an ISR-AP1100AC-B wireless module in slot 0/2, in addition to router and Ethernet-related components.

This matters because an embedded access point may have its own software, storage, and configuration lifecycle. Resetting the host router's startup configuration or formatting the host bootflash does not, by itself, prove that the AP was independently sanitized.`

What this screenshot establishes. An ISR-AP1100AC-B module is visible in the platform listing. This screenshot proves presence, not a separate reset.

Verification checkpoint. Inventory any embedded modules in an approved asset process; independently confirm their reset outcome before calling the entire device sanitized.

Operational caution. Do not assume show platform output proves every subsystem has been examined.

↑ Back to Table of Contents
<a id="step-05"></a>
Step 05 — Establish an interface and addressing baseline
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 05

Objective. Capture the operational status and IPv4 assignments of routed and logical interfaces before further maintenance.

Commands visible in or associated with this capture
Router# show ip interface brief
Technical explanation. show ip interface brief condenses the router's interface configuration into a useful table: interface name, IP address, method, administrative status, and line-protocol state.

Key distinctions: administratively down means an interface has been intentionally shut down; down/down generally indicates it is not operational at the physical/protocol level; up/up means the interface and line protocol report operational. unassigned means no IPv4 address is displayed for that interface. These fields do not require an internet connection to inspect.`

What this screenshot establishes. Most GigabitEthernet interfaces appear unassigned and down; a wireless-related interface and VLAN interface appear up/up in the visible output.

Verification checkpoint. Identify the expected state for each interface. An up/up interface does not establish WAN or internet reachability.

Operational caution. Full IP addresses from customer configurations should be treated as sensitive even when they use private address ranges.

↑ Back to Table of Contents
<a id="step-06"></a>
Step 06 — Review Ethernet interface characteristics and counters
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 06

Objective. Inspect the physical/link-layer baseline, including duplex, speed, MTU, counters, and indications of interface errors.

Commands visible in or associated with this capture
Router# show interfaces
Technical explanation. show interfaces exposes more detail than show ip interface brief: media type, MTU, bandwidth reporting, operational status, interface counters, traffic rates, and error measurements. It may print hardware/MAC addresses, which is why the relevant identifiers were redacted.

A common Ethernet MTU of 1500 bytes describes the usual maximum Layer 3 payload on an untagged Ethernet network. Counters such as input errors, CRC errors, drops, and interface resets can help distinguish cabling, duplex, congestion, and hardware issues. However, a single snapshot cannot establish whether counters are actively increasing.`

What this screenshot establishes. The image includes down interfaces, MTU-related details, inactive/low counters, and masked link-layer addresses.

Verification checkpoint. For a diagnostic investigation, compare counters across two timestamps and correlate with physical link state. Here, only the supplied snapshot is documented.

Operational caution. Do not expose factory MAC addresses or neighboring infrastructure addresses.

↑ Back to Table of Contents
<a id="step-07"></a>
Step 07 — Inspect IPv4 routing state
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 07

Objective. Determine which IPv4 destinations the router can currently forward toward and whether a default route is installed.

Commands visible in or associated with this capture
Router# show ip route
Technical explanation. show ip route lists routes currently installed in the IPv4 routing table. Route source codes help identify how routes were learned: C (connected), L (local), S (static), O (OSPF), and others. Gateway of last resort is not set means the visible IPv4 table has no installed default route.

A routing table can be empty if there are no active Layer 3 networks or learned paths. An empty active table does not prove there are no saved configurations, and it does not guarantee that every attached module is defaulted.`

What this screenshot establishes. The screenshot reports no gateway of last resort and no active destination routes in the displayed portion.

Verification checkpoint. Document that this is an observed active routing state at the moment of the capture, not a certification of full configuration removal.

Operational caution. A router can be safely inspected offline; this command does not require physical WAN connectivity.

↑ Back to Table of Contents
<a id="step-08"></a>
Step 08 — Review the active running configuration
Phase: Configuration inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 08

Objective. Understand the configuration loaded into memory and identify potentially persistent or sensitive settings that need approved handling.

Commands visible in or associated with this capture
Router# show running-config
Technical explanation. show running-config prints the configuration currently active in RAM. It can contain interface descriptions, IP addresses, routing policies, local accounts, access restrictions, service settings, and some secrets in encoded or encrypted form.

Running configuration and startup configuration are not identical concepts. A startup configuration is the saved configuration used at boot; the running configuration reflects the current session. Changes can be active without having been saved, and a recovery boot may ignore an existing startup configuration without deleting it.`

What this screenshot establishes. The image shows the beginning of the active configuration, including IOS XE version-related lines and generic router defaults. It does not show the full configuration.

Verification checkpoint. Treat a complete pre-reset capture as sensitive. Do not make a public claim that every customer setting was absent based on this screenshot alone.

Operational caution. Never publish credentials, password hashes, SNMP strings, VPN endpoints, routing secrets, or customer identifiers—even if they seem obfuscated.

↑ Back to Table of Contents
<a id="step-09"></a>
Step 09 — Enumerate accessible file systems
Phase: Storage assessment
CLI mode: Privileged EXEC
Terminal evidence — Step 09

Objective. Identify where files and configuration artifacts appear in the router's accessible storage namespaces.

Commands visible in or associated with this capture
Router# dir all
Technical explanation. dir all requests a broad directory listing across accessible file systems. Cisco IOS XE platforms can expose multiple prefixes, including transient operating-system file systems and persistent flash-based storage. Different namespaces may have different purposes and access rules.

It is important to distinguish file discovery from data sanitization. Seeing a directory entry establishes only that the device reports it; the listing does not prove the contents were erased or the underlying storage blocks were irrecoverably cleared.`

What this screenshot establishes. The capture begins long listings containing system:/ and tmpsys:/ entries; it does not show every line through the end.

Verification checkpoint. In an authorized environment, collect a complete inventory of relevant persistent stores using supported commands and applicable procedures.

Operational caution. Storage names and layout should be confirmed on the actual model rather than assumed from commands for a different Cisco product.

↑ Back to Table of Contents
<a id="step-10"></a>
Step 10 — Preserve the IOS XE image to USB storage
Phase: Firmware handling
CLI mode: Privileged EXEC
Terminal evidence — Step 10

Objective. Preserve a bootable image on removable media before any authorized maintenance that may affect router storage.

Commands visible in or associated with this capture
Router# dir bootflash:
Router# copy bootflash: usb0:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Technical explanation. dir bootflash: lists the primary boot storage, allowing identification of a candidate Cisco IOS XE image file. copy bootflash: usb0: initiates an interactive file transfer from bootflash to the recognized USB storage device. The router prompts for the exact source and destination filenames.

In the screenshot, the selected image is c1100-universalk9_ias.16.09.05.SPA.bin. Its name corresponds to the C1100 software family; use the file actually present on the device, not an image name copied from another model. The command prefix usb0: is the device name visible for this captured session. Other routers may expose usbflash0: or other names.

A progress line of C characters indicates an ongoing copy operation; it is not a completed-transfer confirmation. Before relying on any backup, an operator should verify file presence and consistency using an approved method and record final command success.`

What this screenshot establishes. The terminal shows dir bootflash:, the interactive USB copy prompts, the selected C1100 image, and transfer progress. No final completion message is visible.

Verification checkpoint. For a fully supported backup claim, capture a successful end-of-copy message and a read-only listing of the destination image including the correct filename and size. Where available, use an authorized integrity check.

Operational caution. A recovery plan is essential before altering the only installed boot image; do not demonstrate destructive storage commands casually on a device you cannot recover.

↑ Back to Table of Contents
<a id="step-11"></a>
Step 11 — Inspect and remove applicable cryptographic keys
Phase: Security handling
CLI mode: Global configuration
Terminal evidence — Step 11

Objective. Document removal of supported key categories where authorized, recognizing that cryptographic material can outlast ordinary interface configuration changes.

Commands visible in or associated with this capture
Router# configure terminal
Router(config)# crypto key zeroize ?
Router(config)# crypto key zeroize ec
Router(config)# crypto key zeroize pubkey-chain
Router(config)# crypto key zeroize rsa
Technical explanation. configure terminal enters global configuration mode. The help form crypto key zeroize ? displays the key categories supported by the currently running software. The captured options include EC (elliptic-curve), a cached public-key chain, and RSA.

zeroize directs the platform to remove applicable stored key material. This is a consequential operation: removing device keys may invalidate certificates and break services that depend on them. A successful key removal should be assessed using the supported feature-specific verification process. It is not the same as validating every credential store, software trust anchor, module, or license state.`

What this screenshot establishes. The screenshot shows key-type help and selected zeroization confirmations; the exact inventory of all remaining keys is not established.

Verification checkpoint. Use applicable authorized checks for relevant key stores. Do not infer complete credential sanitization solely from three key-category commands.

Operational caution. This is destructive credential maintenance. Run it only on equipment whose ownership and permitted maintenance scope are clear.

↑ Back to Table of Contents
<a id="step-12"></a>
Step 12 — Review VTP-domain handling and normal boot behavior
Phase: Configuration maintenance
CLI mode: Global configuration
Terminal evidence — Step 12

Objective. Document platform configuration changes associated with removing inherited switching administration context and returning to a normal boot-register setting.

Commands visible in or associated with this capture
Router# configure terminal
Router(config)# vtp domain null
Router(config)# config-register 0x2102
Router(config)# end
Technical explanation. Where supported, vtp domain null assigns a VTP administrative domain value. VTP (VLAN Trunking Protocol) is a Cisco technology related to distribution of VLAN information between participating switches; the domain is not a generic password or encryption key.

config-register 0x2102 commonly restores the normal behavior of loading a saved startup configuration on future boots. This differs from the recovery setting in Step 1, which was intended to bypass it. The precise effect can be platform and ROMMON dependent, so verification matters.`

What this screenshot establishes. The screenshot shows a previous VTP domain (redacted), an accepted domain change, and a configuration-register setting.

Verification checkpoint. Check the intended register configuration and related operational state on the actual platform. The command changes shown are not independent proof of complete sanitization.

Operational caution. Do not publish the former VTP domain name or infer that a name change removes every VLAN-related setting.

↑ Back to Table of Contents
<a id="step-13"></a>
Step 13 — Remove saved configuration from NVRAM
Phase: Storage and configuration maintenance
CLI mode: Privileged EXEC
Terminal evidence — Step 13

Objective. Document the selected removal of nonvolatile saved configuration information as distinct from live operational state.

Commands visible in or associated with this capture
Router# erase /all nvram:
Router# erase startup-config
Technical explanation. NVRAM is nonvolatile storage historically used to retain configuration across reboots. erase startup-config removes the saved configuration, while erase /all nvram: requests a broader NVRAM-file erase on supported platforms. Cisco displays confirmation prompts because the device's next-boot behavior can change.

These operations are destructive and their exact scope depends on the hardware/software implementation. A completion message for NVRAM is meaningful evidence that the command completed, but it does not establish the status of all persistent storage, including other flash partitions, embedded wireless modules, certificates, or customer data stored elsewhere.`

What this screenshot establishes. The capture includes confirmation and completion output for the selected NVRAM and startup-config operations.

Verification checkpoint. Compare supported configuration and storage inspections with the required authorized acceptance criteria. Avoid claiming that all media have been securely wiped from this step alone.

Operational caution. These are observations from an authorized maintenance workflow—not general commands for experimenting on production hardware.

↑ Back to Table of Contents
<a id="step-14"></a>
Step 14 — Inspect and format bootflash under an approved recovery plan
Phase: Storage and configuration maintenance
CLI mode: Privileged EXEC
Terminal evidence — Step 14

Objective. Document a destructive boot-storage operation and inspection of the resulting directory state.

Commands visible in or associated with this capture
Router# format ?
Router# format bootflash:
Router# dir bootflash:
Technical explanation. format ? displays the file systems that the command may accept on this software build. format bootflash: acts on the router's boot storage; this can remove the installed IOS XE image and make subsequent startup dependent on a valid external image and correct boot configuration. dir bootflash: then lists reported contents after the operation.

Formatting a file system, deleting files, and performing a forensically validated secure erase are not interchangeable. A format-completion message does not by itself prove that all original bytes became unrecoverable, and some system-generated directories may reappear.`

What this screenshot establishes. The screenshot shows supported format targets, a completed bootflash format, and a following directory listing with some generated entries.

Verification checkpoint. For a defensible outcome, the maintenance record must include the required approved storage verification and an independently confirmed restore image.

Operational caution. Do not run format bootflash: on an ordinary working router unless you have explicit authorization and a validated recovery plan.

↑ Back to Table of Contents
<a id="step-15"></a>
Step 15 — Restore the IOS XE image from USB
Phase: Firmware handling
CLI mode: Privileged EXEC
Terminal evidence — Step 15

Objective. Document the attempted return of the preserved Cisco IOS XE image to the router's boot storage.

Commands visible in or associated with this capture
Router# copy usb0: bootflash:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Technical explanation. copy usb0: bootflash: reverses the earlier copy direction and places a selected file from the USB device into bootflash. The exact filename must correspond to the correct router platform and intended image release.

Successful file transfer, successful image integrity validation, correct boot-variable configuration, and a successful post-restart IOS boot are separate checkpoints. Restoring a .bin file to bootflash is an important part of firmware restoration, but it is not enough on its own to prove a fully recovered operational system.`

What this screenshot establishes. The screenshot shows the C1100 image selected as the source and many progress characters. The final transfer completion message is not visible.

Verification checkpoint. An end-of-copy message, destination directory listing, and stable successful IOS XE boot would be needed to substantiate complete firmware restoration.

Operational caution. The available evidence supports “firmware restore initiated / in progress,” not an independently verified successful reimage.

↑ Back to Table of Contents
<a id="step-16"></a>
Step 16 — Inspect post-operation storage listings
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 16

Objective. Compare the accessible file-system view with what is expected after the maintenance work.

Commands visible in or associated with this capture
Router# dir all
Technical explanation. A post-operation dir all provides another look at available filesystems and reported entries. It can help an operator identify unexpected configuration files or image-storage changes. The same output must be interpreted with care: transient system locations and automatically generated files can exist on an otherwise correctly initialized device.

A directory listing is a useful observation, not a standalone sanitization certificate. Directory enumeration cannot reliably describe deleted data remnants in every type of media, and the supplied capture does not include the entire command output.`

What this screenshot establishes. The visible screen includes system:/, tmpsys:/, and a smaller running-config entry; the whole multi-filesystem listing is not shown.

Verification checkpoint. Complete any required platform-specific file-system checks and audit requirements in the authorized workflow.

Operational caution. Do not publish non-redacted directory listings that expose customer filenames, usernames, or configuration names.

↑ Back to Table of Contents
<a id="step-17"></a>
Step 17 — Check VLAN state after maintenance
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 17

Objective. Inspect the embedded switching environment for visible VLAN configuration and normal default VLAN entries.

Commands visible in or associated with this capture
Router# show vlan
Technical explanation. show vlan reports VLAN IDs, names, states, and—in applicable switching environments—associated ports. The screenshot contains the common default VLAN 1 and the legacy default VLAN IDs 1002–1005.

Default VLAN entries are normal on many Cisco switching platforms. Their presence does not automatically mean a customer configuration survived. Conversely, not seeing a custom VLAN in one listing does not independently prove that every switching database or other attached module was wiped.`

What this screenshot establishes. Default VLAN entries appear; no additional named production VLAN is visible in the captured output.

Verification checkpoint. If the security or maintenance requirements cover switching configuration, record the complete approved checks, not just this single command.

Operational caution. Avoid displaying prior VLAN names that refer to customers, sites, departments, or confidential projects.

↑ Back to Table of Contents
<a id="step-18"></a>
Step 18 — Recheck platform modules and request a restart
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 18

Objective. Reassess installed hardware after storage work and initiate a system restart as part of operational validation.

Commands visible in or associated with this capture
Router# show platform
Router# reload
Technical explanation. show platform again inventories the reported component states, providing a comparison with Step 4. reload initiates the router's restart sequence, ordinarily followed by a confirmation prompt. Merely entering the command does not prove that the router rebooted successfully.

The embedded ISR-AP1100AC-B remains visible in the screenshot. Because the module can have its own storage and configuration, successful host-router maintenance does not establish a successful independent AP reset.`

What this screenshot establishes. The capture includes platform entries and the reload request. The subsequent confirmation and complete boot are not visible.

Verification checkpoint. To establish return to service, collect a new post-boot show version, interface check, storage/boot verification, and any required AP-specific outcome.

Operational caution. Do not mark successful startup based solely on an issued reload command.

↑ Back to Table of Contents
<a id="step-19"></a>
Step 19 — Inspect ROMMON environment variables
Phase: Post-operation verification
CLI mode: ROMMON
Terminal evidence — Step 19

Objective. Review boot-monitor variables that can affect startup behavior or retain device-specific settings.

Commands visible in or associated with this capture
rommon 1 > set
Technical explanation. The ROMMON set command prints environment variables used or retained by the boot monitor. Such entries may include boot-related controls and generated numeric values. ROMMON state is distinct from the running IOS XE configuration and deserves its own review when the authorized process requires it.

Environment variables should be interpreted using the correct platform guidance. Some values are normal system state, and not every numeric value is a customer identifier; indiscriminate changes can affect bootability.`

What this screenshot establishes. The screenshot shows ROMMON variable entries with selected numeric values redacted. It does not document a final successful IOS XE boot after this inspection.

Verification checkpoint. Record the intended boot environment under an authorized checklist. A complete end-state claim requires additional evidence of successful startup and any required residual-data checks.

Operational caution. The public repository should not expose unique hardware IDs, serials, or customer-specific ROMMON values.

↑ Back to Table of Contents
<a id="evidence-and-verification-matrix"></a>
7. Evidence And Verification Matrix
The distinction between command observed, operation completed, and final end state proven matters in technical documentation. This table uses only what is visible in the 19 redacted screenshots.
Claim / checkpoint	Relevant screenshots	What is supportable from the images	What is not independently established
ROMMON recovery-mode command entered	step1.png	Register command, reset, boot activity	Successful boot from Step 1 alone
Software and model identified	step2.png, step3.png	C1111-8PWB hardware; IOS XE 16.09.05	All feature licenses and software integrity
Installed modules inspected	step4.png, step18.png	Embedded ISR-AP1100AC-B present	Independent reset of that wireless module
Interface baseline captured	step5.png, step6.png	Selected status, addressing, MAC-masked link statistics	Long-term link health or external reachability
Routing/config baseline inspected	step7.png, step8.png	No visible installed default route; initial config excerpt	Full absence of customer-specific configuration
File systems inspected	step9.png, step16.png	Partial dir all listings	All persistent storage fully enumerated or sanitized
Firmware backup initiated	step10.png	Correct C1100 image selected and transfer progress shown	Complete verified copy on USB
Selected key-removal actions shown	step11.png	Available key types and confirmations	Every relevant credential or key store cleared
Selected NVRAM operations completed	step13.png	Completion messages for shown configuration erasures	Every persistent component securely erased
Bootflash format completed	step14.png	Format-completion message and follow-up listing	Forensic data irrecoverability
Firmware restoration initiated	step15.png	USB-to-bootflash copy progress shown	Full copy confirmation, image integrity, boot success
VLAN defaults inspected	step17.png	Default VLAN entries visible	Complete switch-module or AP sanitization
Reload requested	step18.png	reload entered	Reboot completed successfully
ROMMON variables inspected	step19.png	Selected ROMMON environment shown	Full end-state / all secrets removed


Bottom line: The screenshots show a technically meaningful device-maintenance session with multiple concrete operations. They are not a substitute for a completed formal sanitization audit. If a future authorized lab session produces final copy confirmation, verified boot, AP handling, and additional checks, document those as new evidence rather than retroactively claiming they are already shown.

↑ Back to Table of Contents

<a id="technical-concepts-and-terminology"></a>
8. Technical Concepts And Terminology
Term	Meaning in this project
ROMMON	Cisco's low-level ROM monitor environment for boot and recovery-related control
Configuration register	Setting influencing boot behavior, including whether a saved startup configuration is used
IOS XE	Cisco network operating system installed on this router
Bootflash	Persistent storage namespace used for software images and other files
NVRAM	Nonvolatile storage historically associated with saved configuration information
PID / VID / SN	Product identifier, hardware revision, and unique serial identifier reported by inventory
MAC address	Layer 2 hardware/interface address; redacted from the publication images
MTU	Maximum transmission unit reported by an interface
FCS / CRC	Error-detection mechanisms relevant to Ethernet-interface troubleshooting
Startup configuration	Configuration saved for use during normal boot
Running configuration	Configuration active in the operating system's memory
VTP	Cisco VLAN Trunking Protocol, including domain-related configuration
EC / RSA keys	Asymmetric cryptographic key categories relevant to device certificates and services
Zeroization	Removal of cryptographic key material through supported commands
USB firmware backup	Copy of a software image from device storage to removable media
Firmware restoration	Returning the image to the boot storage and validating operation; validation is not shown complete here
Data sanitization	Controlled removal of retained information, subject to verified scope and acceptance criteria
Verification evidence	Commands, output, timestamps, or audit records supporting a specific claim


↑ Back to Table of Contents

<a id="troubleshooting-lessons-and-common-pitfalls"></a>
9. Troubleshooting Lessons And Common Pitfalls
1. ROMMON is not IOS XE. Command syntax, filesystem visibility, and prompt behavior differ between rommon ... > and Router#. A router that shows ROMMON output has not necessarily booted the expected operating system.

2. A configuration bypass is not a wipe. A recovery register may cause IOS XE to ignore a saved configuration, while that configuration remains physically present until handled separately.

3. USB device names vary. The observed destination/source is usb0:. Do not assume another model uses that prefix. Discover the actual file systems first, and avoid mistaking source and destination when reading copy prompts.

4. File copy progress is not file copy success. Output characters indicate ongoing transfer. A defensible firmware-backup or restoration claim requires visible completion and appropriate destination verification. An independent successful boot is a further checkpoint.

5. Formatting does not automatically prove secure erasure. File-system formatting and physical sanitization are different concepts. Device-specific policy determines what qualifies as complete, and some system files may be recreated.

6. An embedded AP is a separate consideration. The ISR-AP1100AC-B appears in the inventory. Its data-removal status cannot be inferred from host-router commands alone.

7. A clean VLAN table is narrow evidence. Seeing default VLANs is useful but does not prove all switching settings, interfaces, or associated modules are cleared.

8. Never publish raw terminal output unreviewed. show inventory, show interfaces, show running-config, and filesystem listings can expose serials, MAC addresses, site names, interface descriptions, or secrets.

9. Don't claim internet connectivity from up/up. The status refers to the interface/line protocol; upstream service and end-to-end routes require separate tests.

10. Avoid converting the case study into a runbook for unknown devices. A procedure appropriate to one image, feature set, and storage layout may be destructive or incorrect on another model.

↑ Back to Table of Contents

<a id="limits-of-the-available-evidence"></a>
10. Limits Of The Available Evidence
The 19 screenshots do not independently establish all the following outcomes:
- The first USB image backup finished and was verified by filename/size or an integrity check.
- The second USB image restore finished and was verified on bootflash:.
- The restored image completed a clean subsequent IOS XE boot with correct boot variables.
- The embedded ISR-AP1100AC-B was independently reset or had its storage sanitized.
- Every persistent storage namespace, credential store, certificate, log, or license-bearing component was cleared.
- A complete approved sanitization audit was signed off.
Accordingly, this project is best represented as a documented secure-maintenance and firmware-restoration workflow, with the observed steps and their limitations accurately identified. If formal verification exists outside these screenshots, keep it in approved systems and publish only authorized non-sensitive summaries.

↑ Back to Table of Contents

<a id="privacy-safety-and-responsible-publication"></a>
11. Privacy, Safety, And Responsible Publication
- Ownership and authorization: Work only on routers you own or are explicitly authorized to service.
- Confidential material: Do not copy or publish company SOP pages, internal audit records, customer configs, private tickets, or nonpublic documentation without approval. Rewriting a confidential procedure or rearranging its order does not replace permission.
- Screenshot redaction: The images in evidence/ were previously redacted, but visually review them yourself before making the repository public. A redaction review cannot guarantee that every unique identifier was found.
- Protect secrets: Do not include serial numbers, MAC addresses, authentication material, private network details, customer/site names, or live credential data.
- Command risk: crypto key zeroize, erase, and format are destructive. The command blocks in this README explain the observed session; they are not an invitation to run them on unrelated devices.
- No data-erasure certification: A successful command or formatted filesystem alone does not establish full data sanitization. Follow applicable authorized verification criteria.
- No vendor endorsement: This is an independent educational case study, not Cisco documentation or a vendor-approved procedure.
↑ Back to Table of Contents

<a id="repository-layout"></a>
12. Repository Layout
Save this file as README.md in the repository root. Keep the exact screenshot filenames in a sibling evidence directory.
cisco-c1111-secure-sanitization-firmware-restoration/
├── README.md
└── evidence/
    ├── step1.png
    ├── step2.png
    ├── step3.png
    ├── ...
    └── step19.png
GitHub automatically renders the Markdown README and the relative image links as long as the filenames and capitalization match. Upload the README.md, assets/, and evidence/ together at the repository root. GitHub reads the relative image paths directly from the committed PNG files.

↑ Back to Table of Contents

<a id="portfolio-summary"></a>
13. Portfolio Summary
Project: Cisco ISR 1100 — Secure Data Sanitization & IOS XE Firmware Restoration
Hardware: Cisco C1111-8PWB · IOS XE 16.09.05
Areas demonstrated: Cisco CLI, ROMMON, hardware/software identification, Layer 1–3 troubleshooting, filesystem inspection, firmware image transfer, cryptographic key maintenance, configuration handling, VLAN checks, and technical documentation.
Scope: Screenshot-documented operations, with explicit verification gaps.
Interview talking point: “I documented a Cisco ISR 1100 device-maintenance session, identified the IOS XE platform and installed modules, interpreted interface and routing diagnostics, observed firmware transfer and security maintenance operations, and created a redacted evidence-based technical report. I also tracked which outcomes required further verification instead of treating progress output as proof of completion.”

↑ Back to Table of Contents
<div align="center">
  <img src="assets/footer-logo.png" alt="BS IT Professional footer logo" width="170">
</div>
