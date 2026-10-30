<p align="center">
  <img src="assets/brandons-logo.png" alt="IT Professional logo" width="170">
</p><h1 align="center">Cisco ISR 1100 — Secure Data Sanitization &amp; IOS XE Firmware Restoration</h1><p align="center"><strong>Enterprise Network Infrastructure | Cisco C1111-8PWB | IOS XE 16.09.05</strong></p><p align="center">ROMMON · Key Maintenance · Configuration Erasure · Bootflash · USB Firmware Recovery · Infrastructure Validation</p><p align="center">
  <img src="evidence/router.png" alt="Cisco ISR 1100 Series router — front chassis view" width="48%">
  <img src="evidence/router-back.png" alt="Cisco ISR 1100 Series router — rear interface panel" width="48%">
</p>
<p align="center"><sub><strong>Hardware overview:</strong> Front chassis (left) · Rear interface panel (right)</sub></p>Project Summary
This project documents infrastructure maintenance performed on a Cisco ISR 1100 (C1111-8PWB). The work spans device identification, ROMMON and boot settings, interface and route inspection, cryptographic-key handling, configuration and persistent-storage maintenance, and IOS XE image preservation and restoration operations.

The technical record includes 19 redacted console captures and two router hardware photographs, organized by execution stage. Each image appears inline with the corresponding commands, interpretation, and verification criteria. This is a record of observed operations; where the screenshots do not establish a completed end state, that distinction is noted.
Platform	Software	Access	Firmware / Storage	Evidence
Cisco C1111-8PWB	IOS XE 16.09.05	Serial console / ROMMON	bootflash: and usb0:	19 console captures


<a id="table-of-contents"></a>
Table of Contents
1. Project Overview
2. Hardware and Environment
3. Maintenance Objectives
4. CLI Reference
5. Execution Summary
6. Maintenance Procedure and Console Evidence
   - Step 01 — ROMMON and configuration register
   - Step 02 — IOS XE release identification
   - Step 03 — Platform inventory
   - Step 04 — Installed modules
   - Step 05 — Interface and addressing baseline
   - Step 06 — Ethernet interface diagnostics
   - Step 07 — IPv4 routing
   - Step 08 — Running configuration
   - Step 09 — Filesystem inspection
   - Step 10 — IOS XE image backup to USB
   - Step 11 — Cryptographic key maintenance
   - Step 12 — VTP and boot behavior
   - Step 13 — NVRAM configuration removal
   - Step 14 — Bootflash formatting
   - Step 15 — IOS XE image restoration from USB
   - Step 16 — Storage verification
   - Step 17 — VLAN state inspection
   - Step 18 — Module review and reload
   - Step 19 — ROMMON environment review
7. Evidence and Verification Matrix
8. Technical Reference
9. Operational Observations
10. Verification Status
11. Evidence Handling
12. Repository Structure
13. Technical Summary
<a id="project-overview"></a>
1. Project Overview
Operational scope. The maintenance sequence covers device access and platform identification, network and software inspection, firmware image handling, cryptographic key and configuration-related operations, persistent-storage maintenance, and follow-up system checks.

Technical emphasis. Preserve traceability between the command entered, device response, action performed, and verification available from the captured console output. The sequence presented reflects the documented operations on this platform; it is not a generalized procedure for other IOS XE releases or hardware models.

Evidence status. The captures confirm several completed actions, including specific configuration-erasure and filesystem-format operations. Other results, including final image-transfer verification and a clean subsequent boot, are not independently established by the supplied screenshots and remain distinguished in the verification matrix.

↑ Back to Table of Contents

<a id="hardware-and-environment"></a>
2. Hardware and Environment
Component	Recorded details
Device	Cisco ISR 1100, model C1111-8PWB
Operating system	Cisco IOS XE 16.09.05
IOS XE image	c1100-universalk9_ias.16.09.05.SPA.bin
Management access	Serial console, privileged EXEC and ROMMON
Local and removable storage	bootflash: and usb0:
Additional module	Embedded ISR-AP1100AC-B shown in inventory output
Evidence	19 numbered console captures in [`evidence/`](evidence/)


↑ Back to Table of Contents

<a id="objectives-and-skills-demonstrated"></a>
3. Maintenance Objectives
- Identify platform model, installed software, modules, and boot environment.
- Review interface status, addressing, routing, and active configuration.
- Inspect storage locations and identify the installed IOS XE image.
- Preserve and restore firmware using removable storage as documented.
- Perform and record applicable cryptographic-key, saved-configuration, and storage maintenance.
- Inspect post-operation filesystem, VLAN, module, and ROMMON state.
- Retain command-level evidence and identify verification gaps without overstating completion.
↑ Back to Table of Contents

<a id="command-prompt-reference"></a>
4. CLI Reference
Prompt	Environment	Operational purpose
rommon 1 >	ROM monitor	Configuration-register and boot-environment operations
Router>	User EXEC	Basic command access
Router#	Privileged EXEC	Diagnostics, file operations, and reload
Router(config)#	Global configuration	Device-level configuration and key maintenance
Router(config-if)#	Interface configuration	Interface-specific settings where applicable
Source filename []?	CLI input prompt	Interactive source/destination confirmation


Prompts shown in terminal output indicate the current mode and are not part of the command to enter.

↑ Back to Table of Contents

<a id="workflow-at-a-glance"></a>
5. Execution Summary
Phase	Screenshots	Recorded activity
Boot environment	01	ROMMON access and configuration-register change
Platform discovery	02–04	OS release, chassis, and module inventory
Network and configuration inspection	05–08	Interface state, routing, and running configuration
Firmware identification and preservation	09–10	Storage enumeration and USB image copy
Key and storage maintenance	11–14	Cryptographic, NVRAM, and bootflash operations
Image restoration and validation	15–19	USB image return, storage inspections, VLAN/module checks, ROMMON inspection


↑ Back to Table of Contents

<a id="screenshot-by-screenshot-walkthrough"></a>
6. Maintenance Procedure and Console Evidence
The following steps retain the observed command sequence, a direct inline console capture, the technical interpretation, and verification criteria for each stage.

↑ Back to Table of Contents
<a id="step-01"></a>
Step 01 — Access ROMMON and adjust the configuration register
Phase: Boot monitor / access
CLI mode: ROMMON
Terminal evidence — Step 01
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step1.png" alt="Cisco C1111 command-line screenshot — step 01" width="920">
</p>
Objective. Establish a recovery boot path that does not automatically load the previously saved startup configuration. This is an access and boot-behavior change, not a data-erasure action.

Commands visible in or associated with this capture
rommon 1 > confreg 0x2142
rommon 2 > reset
Technical explanation. confreg 0x2142 changes the configuration-register setting so the normal startup configuration is ignored during the subsequent boot. It is commonly used when access to the stored configuration must be bypassed in an authorized recovery workflow. reset restarts the platform from the ROM monitor. ROMMON runs below the normal IOS XE command environment, so commands and prompts differ from those used once IOS starts.

On a Cisco C1111, boot monitor capabilities and prompts can vary by ROMMON release. The literal prompt number (rommon 1 >, rommon 2 >) is a command counter, not a required number you type.

Evidence observation. The console capture shows the ROMMON commands and boot activity identifying the C1111 platform. It does not by itself prove that IOS XE finished booting.

Verification criteria. After the system reaches IOS XE, use show version to confirm the booted release. Later restore an appropriate normal boot-register value; do not leave a recovery-bypass setting unintentionally active.

Operational note. A bypassed startup configuration may remain stored until separately removed. Do not claim sanitization from this step.

↑ Back to Table of Contents
<a id="step-02"></a>
Step 02 — Identify the IOS XE software release
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 02
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step2.png" alt="Cisco C1111 command-line screenshot — step 02" width="920">
</p>
Objective. Identify the operating system and establish a software baseline before assessing commands, storage, and feature support.

Commands visible in or associated with this capture
Router# terminal length 0
Router# show version
Technical explanation. terminal length 0 disables page-at-a-time output for the current terminal session, making it easier to capture complete diagnostic commands. show version reports platform and software details including IOS XE release information, the system image, uptime, and boot history.

The observed device is a Cisco C1111-8PWB, within the Cisco ISR 1100 family, and the visible release is IOS XE 16.09.05. This is important because command availability varies between IOS, IOS XE, ROMMON versions, licenses, and configured modes.

Evidence observation. The visible console output identifies the Cisco C1100 software family and the IOS XE release. It is a platform baseline, not an independent software-integrity check.

Verification criteria. Record the model and release only; redact board IDs, serials, and other persistent identifiers before publishing output.

Operational note. Release identification is not evidence that a particular license or optional feature is enabled.

↑ Back to Table of Contents
<a id="step-03"></a>
Step 03 — Inventory the router chassis and installed components
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 03
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step3.png" alt="Cisco C1111 command-line screenshot — step 03" width="920">
</p>
Objective. Enumerate the physical hardware before determining which components may hold persistent configuration or require separate handling.

Commands visible in or associated with this capture
Router# show inventory
Technical explanation. show inventory enumerates hardware records, generally including PID (product identifier), VID (version identifier), SN (serial number), and descriptive names for modules and components. The PID helps establish that the device is a C1111-8PWB; individual module entries indicate what else may be installed.

Inventory matters for sanitization because a chassis can contain more than one storage-bearing subsystem. A router's main IOS XE environment is not necessarily the only component that may retain configuration.

Evidence observation. The screenshot displays chassis and module entries. Unique serial numbers were masked in the supplied redacted image.

Verification criteria. Confirm the chassis PID and compare any module list with the platform information in the following step.

Operational note. Never post unmasked SN fields, processor board IDs, hardware asset tags, or warranty identifiers.

↑ Back to Table of Contents
<a id="step-04"></a>
Step 04 — Inspect platform slots and the embedded wireless module
Phase: Platform discovery
CLI mode: Privileged EXEC
Terminal evidence — Step 04
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step4.png" alt="Cisco C1111 command-line screenshot — step 04" width="920">
</p>
Objective. Determine which route, switch, and wireless components are physically present and how the system reports their operating states.

Commands visible in or associated with this capture
Router# show platform
Technical explanation. show platform provides a view of the C1111's installed slots and system elements, with state information such as ok or active. In this evidence, the output includes an ISR-AP1100AC-B wireless module in slot 0/2, in addition to router and Ethernet-related components.

This matters because an embedded access point may have its own software, storage, and configuration lifecycle. Resetting the host router's startup configuration or formatting the host bootflash does not, by itself, prove that the AP was independently sanitized.

Evidence observation. An ISR-AP1100AC-B module is visible in the platform listing. This screenshot proves presence, not a separate reset.

Verification criteria. Inventory any embedded modules in an approved asset process; independently confirm their reset outcome before calling the entire device sanitized.

Operational note. Do not assume show platform output proves every subsystem has been examined.

↑ Back to Table of Contents
<a id="step-05"></a>
Step 05 — Establish an interface and addressing baseline
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 05
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step5.png" alt="Cisco C1111 command-line screenshot — step 05" width="920">
</p>
Objective. Capture the operational status and IPv4 assignments of routed and logical interfaces before further maintenance.

Commands visible in or associated with this capture
Router# show ip interface brief
Technical explanation. show ip interface brief condenses the router's interface configuration into a useful table: interface name, IP address, method, administrative status, and line-protocol state.

Key distinctions: administratively down means an interface has been intentionally shut down; down/down generally indicates it is not operational at the physical/protocol level; up/up means the interface and line protocol report operational. unassigned means no IPv4 address is displayed for that interface. These fields do not require an internet connection to inspect.

Evidence observation. Most GigabitEthernet interfaces appear unassigned and down; a wireless-related interface and VLAN interface appear up/up in the visible output.

Verification criteria. Identify the expected state for each interface. An up/up interface does not establish WAN or internet reachability.

Operational note. Full IP addresses from customer configurations should be treated as sensitive even when they use private address ranges.

↑ Back to Table of Contents
<a id="step-06"></a>
Step 06 — Review Ethernet interface characteristics and counters
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 06
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step6.png" alt="Cisco C1111 command-line screenshot — step 06" width="920">
</p>
Objective. Inspect the physical/link-layer baseline, including duplex, speed, MTU, counters, and indications of interface errors.

Commands visible in or associated with this capture
Router# show interfaces
Technical explanation. show interfaces exposes more detail than show ip interface brief: media type, MTU, bandwidth reporting, operational status, interface counters, traffic rates, and error measurements. It may print hardware/MAC addresses, which is why the relevant identifiers were redacted.

A common Ethernet MTU of 1500 bytes describes the usual maximum Layer 3 payload on an untagged Ethernet network. Counters such as input errors, CRC errors, drops, and interface resets can help distinguish cabling, duplex, congestion, and hardware issues. However, a single snapshot cannot establish whether counters are actively increasing.

Evidence observation. The image includes down interfaces, MTU-related details, inactive/low counters, and masked link-layer addresses.

Verification criteria. For a diagnostic investigation, compare counters across two timestamps and correlate with physical link state. Here, only the supplied snapshot is documented.

Operational note. Do not expose factory MAC addresses or neighboring infrastructure addresses.

↑ Back to Table of Contents
<a id="step-07"></a>
Step 07 — Inspect IPv4 routing state
Phase: Network inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 07
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step7.png" alt="Cisco C1111 command-line screenshot — step 07" width="920">
</p>
Objective. Determine which IPv4 destinations the router can currently forward toward and whether a default route is installed.

Commands visible in or associated with this capture
Router# show ip route
Technical explanation. show ip route lists routes currently installed in the IPv4 routing table. Route source codes help identify how routes were learned: C (connected), L (local), S (static), O (OSPF), and others. Gateway of last resort is not set means the visible IPv4 table has no installed default route.

A routing table can be empty if there are no active Layer 3 networks or learned paths. An empty active table does not prove there are no saved configurations, and it does not guarantee that every attached module is defaulted.

Evidence observation. The screenshot reports no gateway of last resort and no active destination routes in the displayed portion.

Verification criteria. Document that this is an observed active routing state at the moment of the capture, not a certification of full configuration removal.

Operational note. A router can be safely inspected offline; this command does not require physical WAN connectivity.

↑ Back to Table of Contents
<a id="step-08"></a>
Step 08 — Review the active running configuration
Phase: Configuration inspection
CLI mode: Privileged EXEC
Terminal evidence — Step 08
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step8.png" alt="Cisco C1111 command-line screenshot — step 08" width="920">
</p>
Objective. Understand the configuration loaded into memory and identify potentially persistent or sensitive settings that need approved handling.

Commands visible in or associated with this capture
Router# show running-config
Technical explanation. show running-config prints the configuration currently active in RAM. It can contain interface descriptions, IP addresses, routing policies, local accounts, access restrictions, service settings, and some secrets in encoded or encrypted form.

Running configuration and startup configuration are not identical concepts. A startup configuration is the saved configuration used at boot; the running configuration reflects the current session. Changes can be active without having been saved, and a recovery boot may ignore an existing startup configuration without deleting it.

Evidence observation. The image shows the beginning of the active configuration, including IOS XE version-related lines and generic router defaults. It does not show the full configuration.

Verification criteria. Treat a complete pre-reset capture as sensitive. Do not make a public claim that every customer setting was absent based on this screenshot alone.

Operational note. Never publish credentials, password hashes, SNMP strings, VPN endpoints, routing secrets, or customer identifiers—even if they seem obfuscated.

↑ Back to Table of Contents
<a id="step-09"></a>
Step 09 — Enumerate accessible file systems
Phase: Storage assessment
CLI mode: Privileged EXEC
Terminal evidence — Step 09
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step9.png" alt="Cisco C1111 command-line screenshot — step 09" width="920">
</p>
Objective. Identify where files and configuration artifacts appear in the router's accessible storage namespaces.

Commands visible in or associated with this capture
Router# dir all
Technical explanation. dir all requests a broad directory listing across accessible file systems. Cisco IOS XE platforms can expose multiple prefixes, including transient operating-system file systems and persistent flash-based storage. Different namespaces may have different purposes and access rules.

It is important to distinguish file discovery from data sanitization. Seeing a directory entry establishes only that the device reports it; the listing does not prove the contents were erased or the underlying storage blocks were irrecoverably cleared.

Evidence observation. The capture begins long listings containing system:/ and tmpsys:/ entries; it does not show every line through the end.

Verification criteria. In an authorized environment, collect a complete inventory of relevant persistent stores using supported commands and applicable procedures.

Operational note. Storage names and layout should be confirmed on the actual model rather than assumed from commands for a different Cisco product.

↑ Back to Table of Contents
<a id="step-10"></a>
Step 10 — Preserve the IOS XE image to USB storage
Phase: Firmware handling
CLI mode: Privileged EXEC
Terminal evidence — Step 10
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step10.png" alt="Cisco C1111 command-line screenshot — step 10" width="920">
</p>
Objective. Preserve a bootable image on removable media before any authorized maintenance that may affect router storage.

Commands visible in or associated with this capture
Router# dir bootflash:
Router# copy bootflash: usb0:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Technical explanation. dir bootflash: lists the primary boot storage, allowing identification of a candidate Cisco IOS XE image file. copy bootflash: usb0: initiates an interactive file transfer from bootflash to the recognized USB storage device. The router prompts for the exact source and destination filenames.

In the screenshot, the selected image is c1100-universalk9_ias.16.09.05.SPA.bin. Its name corresponds to the C1100 software family; use the file actually present on the device, not an image name copied from another model. The command prefix usb0: is the device name visible for this captured session. Other routers may expose usbflash0: or other names.

A progress line of C characters indicates an ongoing copy operation; it is not a completed-transfer confirmation. Before relying on any backup, an operator should verify file presence and consistency using an approved method and record final command success.

Evidence observation. The terminal shows dir bootflash:, the interactive USB copy prompts, the selected C1100 image, and transfer progress. No final completion message is visible.

Verification criteria. For a fully supported backup claim, capture a successful end-of-copy message and a read-only listing of the destination image including the correct filename and size. Where available, use an authorized integrity check.

Operational note. A recovery plan is essential before altering the only installed boot image; do not demonstrate destructive storage commands casually on a device you cannot recover.

↑ Back to Table of Contents
<a id="step-11"></a>
Step 11 — Inspect and remove applicable cryptographic keys
Phase: Security handling
CLI mode: Global configuration
Terminal evidence — Step 11
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step11.png" alt="Cisco C1111 command-line screenshot — step 11" width="920">
</p>
Objective. Document removal of supported key categories where authorized, recognizing that cryptographic material can outlast ordinary interface configuration changes.

Commands visible in or associated with this capture
Router# configure terminal
Router(config)# crypto key zeroize ?
Router(config)# crypto key zeroize ec
Router(config)# crypto key zeroize pubkey-chain
Router(config)# crypto key zeroize rsa
Technical explanation. configure terminal enters global configuration mode. The help form crypto key zeroize ? displays the key categories supported by the currently running software. The captured options include EC (elliptic-curve), a cached public-key chain, and RSA.

zeroize directs the platform to remove applicable stored key material. This is a consequential operation: removing device keys may invalidate certificates and break services that depend on them. A successful key removal should be assessed using the supported feature-specific verification process. It is not the same as validating every credential store, software trust anchor, module, or license state.

Evidence observation. The console capture shows key-type help and selected zeroization confirmations; the exact inventory of all remaining keys is not established.

Verification criteria. Use applicable authorized checks for relevant key stores. Do not infer complete credential sanitization solely from three key-category commands.

Operational note. This is destructive credential maintenance. Run it only on equipment whose ownership and permitted maintenance scope are clear.

↑ Back to Table of Contents
<a id="step-12"></a>
Step 12 — Review VTP-domain handling and normal boot behavior
Phase: Configuration maintenance
CLI mode: Global configuration
Terminal evidence — Step 12
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step12.png" alt="Cisco C1111 command-line screenshot — step 12" width="920">
</p>
Objective. Document platform configuration changes associated with removing inherited switching administration context and returning to a normal boot-register setting.

Commands visible in or associated with this capture
Router# configure terminal
Router(config)# vtp domain null
Router(config)# config-register 0x2102
Router(config)# end
Technical explanation. Where supported, vtp domain null assigns a VTP administrative domain value. VTP (VLAN Trunking Protocol) is a Cisco technology related to distribution of VLAN information between participating switches; the domain is not a generic password or encryption key.

config-register 0x2102 commonly restores the normal behavior of loading a saved startup configuration on future boots. This differs from the recovery setting in Step 1, which was intended to bypass it. The precise effect can be platform and ROMMON dependent, so verification matters.

Evidence observation. The console capture shows a previous VTP domain (redacted), an accepted domain change, and a configuration-register setting.

Verification criteria. Check the intended register configuration and related operational state on the actual platform. The command changes shown are not independent proof of complete sanitization.

Operational note. Do not publish the former VTP domain name or infer that a name change removes every VLAN-related setting.

↑ Back to Table of Contents
<a id="step-13"></a>
Step 13 — Remove saved configuration from NVRAM
Phase: Storage and configuration maintenance
CLI mode: Privileged EXEC
Terminal evidence — Step 13
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step13.png" alt="Cisco C1111 command-line screenshot — step 13" width="920">
</p>
Objective. Document the selected removal of nonvolatile saved configuration information as distinct from live operational state.

Commands visible in or associated with this capture
Router# erase /all nvram:
Router# erase startup-config
Technical explanation. NVRAM is nonvolatile storage historically used to retain configuration across reboots. erase startup-config removes the saved configuration, while erase /all nvram: requests a broader NVRAM-file erase on supported platforms. Cisco displays confirmation prompts because the device's next-boot behavior can change.

These operations are destructive and their exact scope depends on the hardware/software implementation. A completion message for NVRAM is meaningful evidence that the command completed, but it does not establish the status of all persistent storage, including other flash partitions, embedded wireless modules, certificates, or customer data stored elsewhere.

Evidence observation. The capture includes confirmation and completion output for the selected NVRAM and startup-config operations.

Verification criteria. Compare supported configuration and storage inspections with the required authorized acceptance criteria. Avoid claiming that all media have been securely wiped from this step alone.

Operational note. These are observations from an authorized maintenance workflow—not general commands for experimenting on production hardware.

↑ Back to Table of Contents
<a id="step-14"></a>
Step 14 — Inspect and format bootflash under an approved recovery plan
Phase: Storage and configuration maintenance
CLI mode: Privileged EXEC
Terminal evidence — Step 14
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step14.png" alt="Cisco C1111 command-line screenshot — step 14" width="920">
</p>
Objective. Document a destructive boot-storage operation and inspection of the resulting directory state.

Commands visible in or associated with this capture
Router# format ?
Router# format bootflash:
Router# dir bootflash:
Technical explanation. format ? displays the file systems that the command may accept on this software build. format bootflash: acts on the router's boot storage; this can remove the installed IOS XE image and make subsequent startup dependent on a valid external image and correct boot configuration. dir bootflash: then lists reported contents after the operation.

Formatting a file system, deleting files, and performing a forensically validated secure erase are not interchangeable. A format-completion message does not by itself prove that all original bytes became unrecoverable, and some system-generated directories may reappear.

Evidence observation. The console capture shows supported format targets, a completed bootflash format, and a following directory listing with some generated entries.

Verification criteria. For a defensible outcome, the maintenance record must include the required approved storage verification and an independently confirmed restore image.

Operational note. Do not run format bootflash: on an ordinary working router unless you have explicit authorization and a validated recovery plan.

↑ Back to Table of Contents
<a id="step-15"></a>
Step 15 — Restore the IOS XE image from USB
Phase: Firmware handling
CLI mode: Privileged EXEC
Terminal evidence — Step 15
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step15.png" alt="Cisco C1111 command-line screenshot — step 15" width="920">
</p>
Objective. Document the attempted return of the preserved Cisco IOS XE image to the router's boot storage.

Commands visible in or associated with this capture
Router# copy usb0: bootflash:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Technical explanation. copy usb0: bootflash: reverses the earlier copy direction and places a selected file from the USB device into bootflash. The exact filename must correspond to the correct router platform and intended image release.

Successful file transfer, successful image integrity validation, correct boot-variable configuration, and a successful post-restart IOS boot are separate checkpoints. Restoring a .bin file to bootflash is an important part of firmware restoration, but it is not enough on its own to prove a fully recovered operational system.

Evidence observation. The console capture shows the C1100 image selected as the source and many progress characters. The final transfer completion message is not visible.

Verification criteria. An end-of-copy message, destination directory listing, and stable successful IOS XE boot would be needed to substantiate complete firmware restoration.

Operational note. The available evidence supports “firmware restore initiated / in progress,” not an independently verified successful reimage.

↑ Back to Table of Contents
<a id="step-16"></a>
Step 16 — Inspect post-operation storage listings
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 16
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step16.png" alt="Cisco C1111 command-line screenshot — step 16" width="920">
</p>
Objective. Compare the accessible file-system view with what is expected after the maintenance work.

Commands visible in or associated with this capture
Router# dir all
Technical explanation. A post-operation dir all provides another look at available filesystems and reported entries. It can help an operator identify unexpected configuration files or image-storage changes. The same output must be interpreted with care: transient system locations and automatically generated files can exist on an otherwise correctly initialized device.

A directory listing is a useful observation, not a standalone sanitization certificate. Directory enumeration cannot reliably describe deleted data remnants in every type of media, and the supplied capture does not include the entire command output.

Evidence observation. The visible screen includes system:/, tmpsys:/, and a smaller running-config entry; the whole multi-filesystem listing is not shown.

Verification criteria. Complete any required platform-specific file-system checks and audit requirements in the authorized workflow.

Operational note. Do not publish non-redacted directory listings that expose customer filenames, usernames, or configuration names.

↑ Back to Table of Contents
<a id="step-17"></a>
Step 17 — Check VLAN state after maintenance
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 17
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step17.png" alt="Cisco C1111 command-line screenshot — step 17" width="920">
</p>
Objective. Inspect the embedded switching environment for visible VLAN configuration and normal default VLAN entries.

Commands visible in or associated with this capture
Router# show vlan
Technical explanation. show vlan reports VLAN IDs, names, states, and—in applicable switching environments—associated ports. The screenshot contains the common default VLAN 1 and the legacy default VLAN IDs 1002–1005.

Default VLAN entries are normal on many Cisco switching platforms. Their presence does not automatically mean a customer configuration survived. Conversely, not seeing a custom VLAN in one listing does not independently prove that every switching database or other attached module was wiped.

Evidence observation. Default VLAN entries appear; no additional named production VLAN is visible in the captured output.

Verification criteria. If the security or maintenance requirements cover switching configuration, record the complete approved checks, not just this single command.

Operational note. Avoid displaying prior VLAN names that refer to customers, sites, departments, or confidential projects.

↑ Back to Table of Contents
<a id="step-18"></a>
Step 18 — Recheck platform modules and request a restart
Phase: Post-operation verification
CLI mode: Privileged EXEC
Terminal evidence — Step 18
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step18.png" alt="Cisco C1111 command-line screenshot — step 18" width="920">
</p>
Objective. Reassess installed hardware after storage work and initiate a system restart as part of operational validation.

Commands visible in or associated with this capture
Router# show platform
Router# reload
Technical explanation. show platform again inventories the reported component states, providing a comparison with Step 4. reload initiates the router's restart sequence, ordinarily followed by a confirmation prompt. Merely entering the command does not prove that the router rebooted successfully.

The embedded ISR-AP1100AC-B remains visible in the screenshot. Because the module can have its own storage and configuration, successful host-router maintenance does not establish a successful independent AP reset.

Evidence observation. The capture includes platform entries and the reload request. The subsequent confirmation and complete boot are not visible.

Verification criteria. To establish return to service, collect a new post-boot show version, interface check, storage/boot verification, and any required AP-specific outcome.

Operational note. Do not mark successful startup based solely on an issued reload command.

↑ Back to Table of Contents
<a id="step-19"></a>
Step 19 — Inspect ROMMON environment variables
Phase: Post-operation verification
CLI mode: ROMMON
Terminal evidence — Step 19
<p align="center">
  <img src="https://raw.githubusercontent.com/brandon-in-tech/Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/main/evidence/step19.png" alt="Cisco C1111 command-line screenshot — step 19" width="920">
</p>
Objective. Review boot-monitor variables that can affect startup behavior or retain device-specific settings.

Commands visible in or associated with this capture
rommon 1 > set
Technical explanation. The ROMMON set command prints environment variables used or retained by the boot monitor. Such entries may include boot-related controls and generated numeric values. ROMMON state is distinct from the running IOS XE configuration and deserves its own review when the authorized process requires it.

Environment variables should be interpreted using the correct platform guidance. Some values are normal system state, and not every numeric value is a customer identifier; indiscriminate changes can affect bootability.

Evidence observation. The console capture shows ROMMON variable entries with selected numeric values redacted. It does not document a final successful IOS XE boot after this inspection.

Verification criteria. Record the intended boot environment under an authorized checklist. A complete end-state claim requires additional evidence of successful startup and any required residual-data checks.

Operational note. The public repository should not expose unique hardware IDs, serials, or customer-specific ROMMON values.

↑ Back to Table of Contents
<a id="evidence-and-verification-matrix"></a>
7. Evidence and Verification Matrix
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


Verification note. Completed operations and unverified end states are identified separately. The captured outputs should not be interpreted as independent confirmation of every storage or software condition.

↑ Back to Table of Contents

<a id="technical-concepts-and-terminology"></a>
8. Technical Reference
Term	Meaning in this maintenance record
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
9. Operational Observations
1. ROMMON is not IOS XE. Command syntax, filesystem visibility, and prompt behavior differ between rommon ... > and Router#. A router that shows ROMMON output has not necessarily booted the expected operating system.

2. A configuration bypass is not a wipe. A recovery register may cause IOS XE to ignore a saved configuration, while that configuration remains physically present until handled separately.

3. USB device names vary. The observed destination/source is usb0:. Do not assume another model uses that prefix. Discover the actual file systems first, and avoid mistaking source and destination when reading copy prompts.

4. File copy progress is not file copy success. Output characters indicate ongoing transfer. A defensible firmware-backup or restoration claim requires visible completion and appropriate destination verification. An independent successful boot is a further checkpoint.

5. Formatting does not automatically prove secure erasure. File-system formatting and physical sanitization are different concepts. Device-specific policy determines what qualifies as complete, and some system files may be recreated.

6. An embedded AP is a separate consideration. The ISR-AP1100AC-B appears in the inventory. Its data-removal status cannot be inferred from host-router commands alone.

7. A clean VLAN table is narrow evidence. Seeing default VLANs is useful but does not prove all switching settings, interfaces, or associated modules are cleared.

8. Never publish raw terminal output unreviewed. show inventory, show interfaces, show running-config, and filesystem listings can expose serials, MAC addresses, site names, interface descriptions, or secrets.

9. Don't claim internet connectivity from up/up. The status refers to the interface/line protocol; upstream service and end-to-end routes require separate tests.

10. Avoid applying this device-specific sequence to unrelated platforms. A procedure appropriate to one image, feature set, and storage layout may be destructive or incorrect on another model.

↑ Back to Table of Contents

<a id="limits-of-the-available-evidence"></a>
10. Verification Status
The available console captures document the maintenance operations listed above. The following checks are not independently confirmed within the included evidence:
- Successful completion and integrity verification of the USB firmware backup.
- Completed USB-to-bootflash image copy and a verified subsequent IOS XE boot.
- Independent reset or storage processing of the embedded wireless module.
- Full inventory and sanitization of every persistent storage or credential location.
- Formal closeout or independent acceptance testing.
These are evidence limitations, not assertions that the operations failed. Any separately retained acceptance or validation records are outside the scope of this README.

↑ Back to Table of Contents

<a id="privacy-safety-and-responsible-publication"></a>
11. Evidence Handling
The repository uses redacted console captures and excludes identifiers, credentials, device-specific secrets, and operationally sensitive configuration details. Only material cleared for publication should be included. Destructive operations documented here are device-specific and should be performed under applicable maintenance controls.

↑ Back to Table of Contents

<a id="repository-layout"></a>
12. Repository Structure
Cisco-ISR-1100-Secure-Data-Sanitization-IOS-XE-Firmware-Restoration/
├── README.md
├── assets/
│   ├── brandons-logo.png
│   └── footer-logo.png
└── evidence/
    ├── router.png
    ├── router-back.png
    ├── step1.png
    ├── step2.png
    ├── ...
    └── step19.png
Both hardware photographs appear in the project header, and every console capture is embedded in its corresponding procedure section. The assets/ directory contains the project header and footer branding.

↑ Back to Table of Contents

<a id="technical-summary"></a>
13. Technical Summary
The recorded work covers Cisco ISR boot-environment management, IOS XE firmware handling, configuration and cryptographic-key maintenance, bootflash and NVRAM operations, and Layer 1–3 infrastructure diagnostics. Nineteen redacted terminal captures document the command sequence and available device responses. The verification matrix distinguishes completed actions from end states that require additional supporting records.

↑ Back to Table of Contents
<div align="center">
  <img src="assets/footer-logo.png" alt="IT Professional footer logo" width="170">
</div>
