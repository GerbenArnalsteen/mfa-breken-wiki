---
title: "Victim-VM"
nav_order: 3
parent: "Lab"
---

# Victim-VM

| Stap | Actie | Aandachtspunt |
|---|---|---|
| 1 | VM aanmaken met de Windows 11 Enterprise Evaluation-ISO | — |
| 2 | VM-encryptie activeren ("Only the files needed to support TPM are encrypted") | Vereist vóór stap 3; bewaar het wachtwoord gedeeld |
| 3 | vTPM-apparaat toevoegen | — |
| 4 | OOBE doorlopen met een lokaal account | Windows 11 dwingt een Microsoft-account af bij netwerkverbinding. Oplossing: netwerkadapter loskoppelen vóór de eerste start |
| 5 | Entra-join uitvoeren | Controle met `dsregcmd /status`: `AzureADJoined: YES`, `AzureADPRT: YES` |
| 6 | Lokale adminrechten toewijzen | Zie 3.4; volledig af- en aanmelden |
| 7 | WHFB-policy laten synchroniseren en PIN registreren | Controle: `NgcSet: YES` met gevulde `KeyId` |
| 8 | Snapshot maken in schone staat | Ook vóór elke destructieve test |

## Controle na de opbouw

```
dsregcmd /status
whoami /groups
Get-Tpm | Select-Object TpmPresent,TpmReady,LockedOut,LockoutCount,LockoutMax,LockoutHealTime
```
