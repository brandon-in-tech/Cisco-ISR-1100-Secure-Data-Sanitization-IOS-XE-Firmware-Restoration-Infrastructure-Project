<p align="center">
  <img src="assets/brandons-logo.png" alt="Brandon IT Professional logo" width="155">
</p><h1 align="center">Cisco ISR 1100 — Secure Data Sanitization &amp; IOS XE Firmware Restoration</h1><p align="center"><strong>Cisco C1111-8PWB · IOS XE 16.09.05 · Enterprise Network Infrastructure</strong></p><p align="center">
  <img src="evidence/router.png" alt="Cisco ISR 1100 front view" width="48%">
  <img src="evidence/router-back.png" alt="Cisco ISR 1100 rear interface view" width="48%">
</p>
<p align="center"><sub>Front and rear views of the Cisco ISR 1100</sub></p>Overview
This project documents Cisco ISR 1100 maintenance involving ROMMON recovery, IOS XE image backup and restoration, cryptographic-key handling, configuration removal, storage maintenance, and device diagnostics. The sequence is supported by 19 redacted console screenshots.
Hardware	Operating system	Access	Storage
Cisco C1111-8PWB	IOS XE 16.09.05	Serial console / ROMMON	bootflash: · usb0:


Table of Contents
- Overview
- Maintenance Procedure
  - Step 01 — ROMMON configuration register
  - Step 02 — IOS XE version verification
  - Step 03 — Hardware inventory
  - Step 04 — Platform and wireless module inspection
  - Step 05 — Interface status
  - Step 06 — Ethernet diagnostics
  - Step 07 — IPv4 routing table
  - Step 08 — Running configuration
  - Step 09 — Filesystem inventory
  - Step 10 — IOS XE image backup to USB
  - Step 11 — Cryptographic key maintenance
  - Step 12 — VTP and boot-register configuration
  - Step 13 — NVRAM and startup-config removal
  - Step 14 — Bootflash formatting
  - Step 15 — IOS XE image restoration
  - Step 16 — Post-operation filesystem review
  - Step 17 — VLAN inspection
  - Step 18 — Module review and reload
  - Step 19 — ROMMON environment review
- Results and Verification
- Repository Structure
Maintenance Procedure
The steps below follow the recorded command sequence. Commands and screenshots are included directly under each step.

<a id="step-01"></a>
Step 01 — ROMMON configuration register
rommon 1 > confreg 0x2142
rommon 2 > reset
Set the recovery register to bypass the saved startup configuration and restarted the router from ROMMON. This changes boot behavior; it does not erase the saved configuration.

↑ Back to Table of Contents
<a id="step-02"></a>
Step 02 — IOS XE version verification
Router# terminal length 0
Router# show version
Identified the C1111 platform and IOS XE 16.09.05. Disabled terminal pagination to capture the diagnostic output.

↑ Back to Table of Contents
<a id="step-03"></a>
Step 03 — Hardware inventory
Router# show inventory
Reviewed chassis and installed component records, including product identifiers. Unique hardware identifiers are redacted in the evidence.

↑ Back to Table of Contents
<a id="step-04"></a>
Step 04 — Platform and wireless module inspection
Router# show platform
Checked reported module states and identified the embedded ISR-AP1100AC-B wireless module. Its presence does not establish that the module was separately reset.

↑ Back to Table of Contents
<a id="step-05"></a>
Step 05 — Interface status
Router# show ip interface brief
Reviewed interface addresses and administrative and protocol states. Most visible Ethernet interfaces are unassigned/down; an up/up status alone does not confirm network reachability.

↑ Back to Table of Contents
<a id="step-06"></a>
Step 06 — Ethernet diagnostics
Router# show interfaces
Inspected link status, MTU, interface counters, and error statistics. Hardware addresses were masked in the screenshot.

↑ Back to Table of Contents
<a id="step-07"></a>
Step 07 — IPv4 routing table
Router# show ip route
Checked the active routing table. The captured output reports no gateway of last resort; this is a point-in-time routing observation.

↑ Back to Table of Contents
<a id="step-08"></a>
Step 08 — Running configuration
Router# show running-config
Reviewed the active configuration. The screenshot contains only a portion of the output; the running and saved startup configurations are separate.

↑ Back to Table of Contents
<a id="step-09"></a>
Step 09 — Filesystem inventory
Router# dir all
Enumerated accessible filesystem entries before storage operations. The console capture shows a partial listing.

↑ Back to Table of Contents
<a id="step-10"></a>
Step 10 — IOS XE image backup to USB
Router# dir bootflash:
Router# copy bootflash: usb0:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Selected the C1100 IOS XE image and initiated transfer from bootflash: to usb0:. Copy progress is visible, but final completion is not shown in the screenshot.

↑ Back to Table of Contents
<a id="step-11"></a>
Step 11 — Cryptographic key maintenance
Router# configure terminal
Router(config)# crypto key zeroize ?
Router(config)# crypto key zeroize ec
Router(config)# crypto key zeroize pubkey-chain
Router(config)# crypto key zeroize rsa
Reviewed supported key categories and executed the recorded EC, public-key-chain, and RSA zeroization commands. These operations do not independently verify every credential store.

↑ Back to Table of Contents
<a id="step-12"></a>
Step 12 — VTP and boot-register configuration
Router# configure terminal
Router(config)# vtp domain null
Router(config)# config-register 0x2102
Router(config)# end
Changed the VTP domain setting and restored the normal configuration-register value for subsequent boots.

↑ Back to Table of Contents
<a id="step-13"></a>
Step 13 — NVRAM and startup-config removal
Router# erase /all nvram:
Router# erase startup-config
Removed the selected nonvolatile configuration data. Completion messages for these commands are visible in the console evidence.

↑ Back to Table of Contents
<a id="step-14"></a>
Step 14 — Bootflash formatting
Router# format ?
Router# format bootflash:
Router# dir bootflash:
Formatted bootflash: and inspected the resulting directory. The format operation completed, but formatting alone is not proof of forensic data sanitization.

↑ Back to Table of Contents
<a id="step-15"></a>
Step 15 — IOS XE image restoration
Router# copy usb0: bootflash:
Source filename []? c1100-universalk9_ias.16.09.05.SPA.bin
Destination filename [c1100-universalk9_ias.16.09.05.SPA.bin]?
Initiated the return transfer of the IOS XE image from USB to internal storage. The screenshot shows progress but not final copy confirmation or a subsequent successful boot.

↑ Back to Table of Contents
<a id="step-16"></a>
Step 16 — Post-operation filesystem review
Router# dir all
Inspected reported filesystem entries after maintenance. The captured output is a partial listing and does not establish full-media sanitization.

↑ Back to Table of Contents
<a id="step-17"></a>
Step 17 — VLAN inspection
Router# show vlan
Reviewed the reported VLAN state. Default VLAN entries are visible, with no additional production VLAN names shown in this capture.

↑ Back to Table of Contents
<a id="step-18"></a>
Step 18 — Module review and reload
Router# show platform
Router# reload
Rechecked installed hardware and issued a reload request. The screenshot does not show the full restart or verified IOS XE startup.

↑ Back to Table of Contents
<a id="step-19"></a>
Step 19 — ROMMON environment review
rommon 1 > set
Inspected ROMMON environment variables and boot-related state. The capture does not document a final successful post-maintenance IOS XE boot.

↑ Back to Table of Contents
Results and Verification
The recorded console output confirms the router model and software version, platform and network inspections, selected key-management operations, configuration-erasure completion, and bootflash formatting. IOS XE image backup and restore transfers were initiated and progress was captured.

Evidence limits: The screenshots do not independently show completed image-transfer verification, a successful final IOS XE boot, or separate sanitization of the embedded wireless module. These are not represented as verified outcomes.

↑ Back to Table of Contents
Repository Structure
README.md
assets/
  brandons-logo.png
  footer-logo.png
evidence/
  router.png
  router-back.png
  step1.png ... step19.png
The console images have been redacted for publication. Operational identifiers and confidential configuration details are not included.

↑ Back to Table of Contents
<p align="center">
  <img src="assets/footer-logo.png" alt="IT Professional footer logo" width="150">
</p>
