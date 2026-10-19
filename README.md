# 🔍 Investigating Memory Evidence

![Discipline](https://img.shields.io/badge/Discipline-Digital%20Forensics-blue)
![Evidence](https://img.shields.io/badge/Evidence-Memory%20Forensics-purple)
![Platform](https://img.shields.io/badge/Platform-Windows%20Memory-lightgrey)
![Status](https://img.shields.io/badge/Status-Investigation%20Artefacts%20Preserved-orange)

## 🧭 Project Overview

This repository contains the forensic artefacts and investigation outputs generated for an SBT-DF204 digital forensics case study concerning suspected unauthorised file transfer activity.

The investigation examines a supplied Windows memory image to identify evidence of process execution, command-line activity, network configuration and connections, user profiles, registry artefacts, and removable storage. Particular attention is given to a command recorded in the console history that appears to copy a file named `secret_file.docx` to drive `F:`.

The investigation uses memory-forensics techniques to examine information preserved in volatile memory and associated Windows registry structures. The purpose is to establish what the available artefacts support, correlate relevant observations, document limitations, and avoid drawing conclusions beyond the evidence.

The repository preserves the working artefacts and outputs used during the investigation. It is intended to support technical review, reproducibility, and transparent documentation of the investigative process.

## 🎯 Investigation Objectives

The investigation focuses on the following objectives:

- Identify the operating system characteristics and suitable Volatility profile for the supplied memory image.
- Examine active processes and process relationships to identify potentially relevant applications and command interpreters.
- Recover available console and command-history artefacts.
- Examine network artefacts for relevant addresses, listening services, and connection records.
- Identify registry evidence associated with removable storage and drive-letter assignments.
- Examine user-profile and Explorer-related registry artefacts where supported by available tools.
- Correlate relevant timestamps into an investigative timeline using UTC.
- Assess whether the artefacts support a suspected file-copy event and clearly document what cannot be established from memory evidence alone.
- Preserve forensic outputs and document technical limitations that may affect interpretation.

## 🧰 Tools and Techniques

### Volatility 2

Volatility 2 was used to analyse the supplied memory image. The investigation included the following plugins and supporting commands:

| Tool or plugin | Investigative purpose |
|---|---|
| `imageinfo` | Identify candidate operating-system profiles and memory-image metadata |
| `pslist` | Enumerate process objects available through the active process list |
| `psscan` | Search memory for process structures, subject to plugin execution limitations |
| `pstree` | Examine parent-child process relationships |
| `cmdline` | Recover available process command-line information |
| `cmdscan` | Examine command-history artefacts |
| `consoles` | Recover available console metadata, command history, and screen-buffer contents |
| `netscan` | Examine available network objects and connection records |
| `hivelist` | Identify registry hives available in memory |
| `printkey` | Examine selected registry keys and values |
| Supporting shell commands | Verify file sizes, calculate SHA-256 hashes, inventory outputs, and manage the investigation workspace |
| Git and Git LFS | Version and publish project artefacts and large files, subject to repository configuration and upload limits |

Plugin output is interpreted in context. A missing result does not necessarily establish that an event or artefact never existed.

## 🗂️ Repository Structure

The investigation artefacts are organised by evidence type and analytical purpose.

```text
Investigating-Memory-Evidence/
├── .gitattributes
├── README.md
├── evidence/
│   ├── original/
│   │   └── memdumpWin7.mem
│   └── working/
│       ├── memdumpWin7.mem
│       └── memdumpWin7.mem.incomplete
├── investigation/
│   ├── notes/
│   ├── output/
│   │   └── imageinfo.txt
│   ├── query-results/
│   │   ├── cmdline.txt
│   │   ├── cmdscan.txt
│   │   ├── consoles.txt
│   │   ├── hivelist.txt
│   │   ├── imageinfo.txt
│   │   ├── memory_file_sizes.txt
│   │   ├── memory_sha256.txt
│   │   ├── mounted_devices.txt
│   │   ├── mountpoints2.txt
│   │   ├── mountpoints2_f.txt
│   │   ├── mountpoints2_usb_shell.txt
│   │   ├── mountpoints2_usb_volume.txt
│   │   ├── netscan.txt
│   │   ├── output_inventory.txt
│   │   ├── profilelist.txt
│   │   ├── profile_sid1000.txt
│   │   ├── profile_sid1002.txt
│   │   ├── pslist.txt
│   │   ├── psscan.txt
│   │   ├── pstree.txt
│   │   ├── recentdocs.txt
│   │   ├── recentdocs_docx.txt
│   │   ├── runmru.txt
│   │   ├── shellbags_plugin_check.txt
│   │   ├── typedpaths.txt
│   │   ├── usb_stor.txt
│   │   ├── usb_stor_device_details.txt
│   │   └── usb_stor_instance_details.txt
│   └── timeline/
├── report/
└── screenshots/
```

Directory placeholders may be present to retain the intended project structure. The final report, screenshots, and formal timeline should be added as they are completed.

### 📁 Evidence directories

**`evidence/original/`**

Contains the preserved original memory image used as the reference copy for integrity verification.

**`evidence/working/`**

Contains the working memory image used for analysis. The directory also retains an incomplete copy produced during an earlier copy attempt. The incomplete file is not a valid substitute for the verified memory image and must not be treated as authoritative evidence.

### 📄 Investigation outputs

**`investigation/query-results/`**

Contains saved output from the memory-forensics commands and registry investigations. These files allow the findings to be reviewed without rerunning every command immediately.

**`investigation/output/`**

Contains a supplementary output location. The existing `imageinfo.txt` in this directory is empty; the populated image-identification output is located under `investigation/query-results/`.

**`investigation/notes/` and `investigation/timeline/`**

Reserved for interpretive notes, evidence correlations, and a consolidated event timeline.

**`report/` and `screenshots/`**

Reserved for the formal investigation report and supporting screenshots.

## 🧪 Evidence Acquisition and Integrity

The supplied memory image was retained in separate original and working locations to support evidence preservation and analysis.

During the preparation of the working evidence, an incomplete copy of 84,250,624 bytes was identified. This copy was not accepted as the valid working image. The working copy was recreated and its integrity was checked against the original.

The final verification recorded the following details:

| Property | Verified value |
|---|---|
| Original image | `evidence/original/memdumpWin7.mem` |
| Working image | `evidence/working/memdumpWin7.mem` |
| Size of each complete image | 1,073,676,288 bytes |
| SHA-256 | `500bc1ba8b5ffa8053b6213d7e7aae0c84c18cce541dc53a55887e6e5099495e` |
| Integrity result | Matching hashes at the time of verification |

The matching SHA-256 hashes establish that the original and working files were byte-for-byte identical at the time of verification.

The earlier incomplete copy remains distinguishable by its filename and smaller size. It should not be used for forensic analysis or represented as a complete acquisition.

The acquisition record also noted that the temporary download location was no longer available during final verification. Consequently, the final verification compared the preserved original and working files rather than rechecking the unavailable temporary file.

## 🖥️ Memory Image Identification and Profile Selection

The Volatility `imageinfo` plugin was used to examine the supplied image and identify candidate memory profiles.

The saved output identified `Win7SP1x86` among the candidate profiles, and this profile was selected for the subsequent analysis. The reported image timestamp was:

- **UTC:** `2019-01-06 15:09:06`
- **Reported local time:** `2019-01-06 07:09:06 -0800`

The image metadata indicated a 32-bit Windows memory layout using PAE address translation.

**Important operating-system discrepancy:** the assignment scenario refers to Windows 10, while the supplied memory image and the profile identification results are consistent with Windows 7 SP1 x86. The analysis therefore documents the image characteristics observed in the supplied evidence rather than assuming the scenario description accurately identifies the operating system.

The `imageinfo` output provides candidate profiles; it does not, by itself, conclusively establish the operating-system version. Profile selection should be interpreted alongside the other results and the limitations of the available image.

## 🔎 Preliminary Findings

### 💻 Process and execution artefacts

The saved process outputs identify several processes relevant to the investigation, including `cmd.exe`, `explorer.exe`, `FTK Imager.exe`, `chrome.exe`, and OpenSSH-related processes.

The process tree records `cmd.exe` as a child of `explorer.exe`. The saved command-line output identifies the command interpreter and the FTK Imager executable.

The presence of these processes establishes that their corresponding process artefacts were available in the memory image. It does not, on its own, establish malicious intent or prove that a file was transferred.

The `psscan` output was affected by dependency warnings. Its results therefore cannot be treated as a complete or definitive inventory of hidden, terminated, or residual processes.

### ⌨️ Command history and suspected file copy

The strongest direct artefact recovered during the investigation is a command recorded in the console history:

```text
C:\Users\IEUser>cd Downloads
C:\Users\IEUser\Downloads>copy secret_file.docx F:
1 file(s) copied.
```

The console history also contains an `ipconfig` command, with output identifying the local address `192.168.56.8`.

The recorded copy command and the subsequent console message support the assessment that the console recorded a successful file-copy operation targeting drive `F:`.

However, this evidence alone does not establish the contents of the destination file, whether the file was confidential, whether the copy was authorised, or the identity of the person who issued the command. The recovered console output also does not provide an exact, independently verified timestamp for the copy command itself.

### 🔌 Removable storage and drive-letter artefacts

Registry artefacts recovered from the SYSTEM hive identify a USB storage device described as:

```text
General UDisk USB Device
```

The USBSTOR information contains the device and instance identifiers. The `MountedDevices` artefacts associate drive `F:` with a USBSTOR device path, while the user's `MountPoints2` artefacts contain a related volume GUID.

The relevant registry-key update times include:

- **15:02:54 UTC:** USBSTOR and MountedDevices key updates.
- **15:03:06 UTC:** a MountPoints2 `F` key update.
- **15:03:07 UTC:** a related volume-GUID MountPoints2 key update.

These records are consistent with removable storage being represented in the system's registry. Registry update times are not necessarily the exact time a device was inserted, a file was copied, or a user interacted with the device.

The association between the recorded `F:` copy destination and the USB storage artefacts is an important investigative correlation, but it should not be described as independent proof of the destination file's contents.

### 🌐 Network artefacts

The saved `netscan` output records an OpenSSH `sshd.exe` process listening on TCP port 22, along with other network objects.

Two closed TCP records associate the local address `192.168.56.8` with `192.168.56.5` on port 80. The records are marked `CLOSED` and do not independently establish that a successful file transfer occurred over the network.

The available network evidence does not conclusively demonstrate that `secret_file.docx` was transferred to a remote system. The console copy command and removable-storage registry artefacts are more directly relevant to the suspected copy to drive `F:`.

### 👤 User profiles and registry artefacts

The recovered registry information identifies profile paths including:

```text
C:\Users\IEUser
C:\Users\sshd_server
```

The `ProfileList` and associated user-hive outputs provide context for the user profiles represented in the memory image. OpenSSH-related processes and the `sshd_server` profile are relevant context for further examination.

These artefacts do not, on their own, establish which individual was physically operating the system or whether a remote user authenticated successfully.

### 🗃️ Recent-document and Explorer-related artefacts

The available RecentDocs output includes entries related to scripts, text files, Windows utilities, and system locations. The saved `.docx` RecentDocs query did not identify a corresponding key in the examined user hive.

The absence of `secret_file.docx` from that particular output does not prove that the file was never present, opened, or copied. Registry artefacts may be incomplete, unavailable in the captured memory, or recorded in locations not covered by the query.

The ShellBags plugin could not be imported because of a missing `Crypto.Hash` dependency. ShellBags analysis was therefore not completed successfully, and no conclusion should be drawn about the presence or absence of relevant ShellBags artefacts.

## 🕒 Timeline Correlation

The following observations provide a preliminary chronology based on the saved process and registry outputs.

| Time (UTC) | Artefact | Observation |
|---|---|---|
| 15:02:54 | USBSTOR / MountedDevices | Relevant registry keys were last updated |
| 15:03:06 | MountPoints2 | `F` key update recorded |
| 15:03:07 | MountPoints2 | Related volume-GUID key update recorded |
| 15:06:11 | Process output | `cmd.exe` creation time recorded |
| 15:07:15 | Process output | `FTK Imager.exe` creation time recorded |
| 15:08:32 | Network output | Chrome UDP network object creation time recorded |
| 15:09:06 | Image metadata | Memory image timestamp reported |

The chronology is provisional. The timestamps originate from different artefact types and do not all represent the same kind of event. In particular, registry last-write times should not automatically be treated as exact device insertion times, and process creation times do not establish the exact time at which a command was entered.

The recovered console history does not provide a reliable command-specific timestamp for the copy operation. Accordingly, the copy command should not be assigned an exact time merely because it appears alongside other console and process artefacts.

## ⚖️ Assessment and Evidential Limitations

The available evidence supports the following cautious assessment:

- The recovered console history contains a command to copy `secret_file.docx` to drive `F:`, followed by the message `1 file(s) copied.`
- Registry artefacts associate drive `F:` with a General UDisk USB storage device.
- The command-history and registry findings are consistent with a possible file copy to removable storage.
- The available outputs do not establish whether the activity was authorised, whether the file contained sensitive information, or who personally performed the operation.
- The network records do not independently establish a successful remote file transfer.
- Some analysis was limited by missing plugin dependencies, and the supplied image's operating-system characteristics differ from the assignment scenario's description.

The distinction between an observed artefact and its interpretation is essential. The strongest conclusion is that the memory evidence contains a recorded copy command and related removable-storage artefacts. A definitive conclusion about unauthorised exfiltration or individual attribution would require additional corroborating evidence.

## ⚠️ Technical Limitations

Several limitations should be considered when interpreting the investigation:

- **Operating-system discrepancy:** the scenario refers to Windows 10, while the supplied image is consistent with Windows 7 SP1 x86.
- **Plugin dependencies:** the ShellBags plugin could not be imported because `Crypto.Hash` was unavailable in the Python 2 environment.
- **Process scanning:** dependency warnings affected the usability of `psscan`, preventing a definitive conclusion about hidden or residual processes.
- **Memory scope:** a memory image represents a point-in-time acquisition and may not contain every relevant file, registry value, event, or historical record.
- **Timestamp interpretation:** registry updates, process creation times, and image timestamps describe different events and should not be treated as interchangeable.
- **Attribution:** process, console, and profile artefacts do not independently identify the human responsible for the activity.
- **Transfer confirmation:** the available evidence does not independently establish the final contents or integrity of a destination file on the USB device.
- **Report completeness:** the repository's formal report, screenshots, and consolidated timeline should be added when completed.

## 📚 Evidence Artefacts and Reproducibility

The saved query results provide a record of the commands and outputs examined during the investigation.

For review, begin with these files:

- `investigation/query-results/imageinfo.txt` — memory-image identification and candidate profiles.
- `investigation/query-results/memory_sha256.txt` — recorded memory-image hashes.
- `investigation/query-results/pslist.txt` — process listing.
- `investigation/query-results/pstree.txt` — process relationships.
- `investigation/query-results/cmdline.txt` — available process command-line information.
- `investigation/query-results/cmdscan.txt` — command-history artefacts.
- `investigation/query-results/consoles.txt` — console history and screen-buffer output.
- `investigation/query-results/netscan.txt` — network artefacts.
- `investigation/query-results/usb_stor.txt` — USB storage enumeration artefacts.
- `investigation/query-results/usb_stor_device_details.txt` — selected USB device details.
- `investigation/query-results/usb_stor_instance_details.txt` — USB instance information.
- `investigation/query-results/mounted_devices.txt` — mounted-device registry information.
- `investigation/query-results/mountpoints2.txt` — user MountPoints2 artefacts.
- `investigation/query-results/profilelist.txt` — user-profile registry information.
- `investigation/query-results/shellbags_plugin_check.txt` — record of the ShellBags plugin dependency issue.

These outputs are intended to support review of the investigation. Reproducibility depends on using a compatible analysis environment, the appropriate memory profile, and the relevant plugin dependencies.

## 🔐 Evidence Handling and Publication

This repository is public. Memory images and forensic outputs can contain personal information, browser artefacts, credentials, or other sensitive material.

The presence of an assignment on a public platform does not automatically establish permission to redistribute every associated evidence file. Anyone maintaining or reproducing this project should confirm that they have permission to publish the supplied image and any derived data.

Large memory files are configured for Git Large File Storage (Git LFS). Availability of those files to repository visitors depends on successful upload, repository access, and applicable LFS storage and bandwidth limits.

The file `memdumpWin7.mem.incomplete` is retained as an incomplete-copy artefact and must not be used as the authoritative memory image.

## 🏁 Conclusion

This project documents a memory-forensics investigation into suspected file-copy activity involving removable storage. The principal finding is a recovered console-history entry showing a copy command for `secret_file.docx` to drive `F:`, followed by a console message reporting that one file was copied. Registry artefacts independently associate drive `F:` with a General UDisk USB storage device.

Taken together, these observations support further investigation of a possible copy to removable storage. They do not, by themselves, establish unauthorised data exfiltration, the sensitivity of the file, or the identity of the person responsible.

The investigation preserves the distinction between direct observations, supporting correlations, and unresolved questions. Its findings should be interpreted within the technical and evidential limitations documented in this repository.

---

*Prepared as part of an SBT-DF204 digital forensics case study. All interpretations are limited to the artefacts examined and the analysis outputs preserved in this repository.*
