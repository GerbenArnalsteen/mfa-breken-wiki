---
title: "PB-02 PRT-sleutels exporteren"
nav_order: 2
parent: "Playbooks"
---

# PB-02 PRT-sleutels exporteren

| | |
|---|---|
| **Pad** | B, sub-aanpak 1 |
| **Doel** | De PRT en de toestelsleutels van een beheerd toestel exporteren |
| **Tooling** | AADInternals v0.9.8 |
| **Waar** | Victim, PowerShell 7 als beheerder |
| **Status** | Getest — geblokkeerd |

**Startcondities.** Victim-VM, Entra-joined, vTPM, WHFB-PIN, volledig door Intune beheerd; aangemeld als `28_tu_whbf` met lokale adminrechten; PowerShell 7 als beheerder; AADInternals v0.9.8. Datum: 12/11/2026.

| Methode | Resultaat | Oorzaak |
|---|---|---|
| `Get-AADIntUserPRTKeysFromCloudAP` | Helper-programma verwijderd door antivirus | Defender herkent het gegenereerde programma op signatuur, niet op naam |
| `Export-AADIntLocalDeviceCertificate` | "Key not found" | De sleutel zit in de TPM en is niet exporteerbaar |
| `Export-AADIntLocalDeviceTransportKey` | "Unable to get SoftwareTransportKeyName" | Zelfde oorzaak: TPM-binding |

Vier lagen werkten hier samen: Windows Defender verwijdert offensieve programma's; Tamper Protection vergrendelt de Defender-instellingen, ook voor een lokale beheerder (`Set-MpPreference -DisableRealtimeMonitoring $true` gaf geen fout maar had geen effect); Intune vergrendelt de beveiligingsinstellingen; de TPM geeft de privésleutels niet vrij. Ook nadat de antiviruslaag via een Intune-exclusie was weggenomen, bleef de extractie mislukken op de TPM-binding. Dat is het bedoelde gedrag van de TPM.

## Commando's

```
Get-AADIntUserPRTKeysFromCloudAP
Export-AADIntLocalDeviceCertificate
Export-AADIntLocalDeviceTransportKey
```

Voor de Defender-exclusies die nodig zijn om de tooling te laten draaien: zie [Tenant en policies]({{ '/lab/tenant-en-policies/' | relative_url }}).
