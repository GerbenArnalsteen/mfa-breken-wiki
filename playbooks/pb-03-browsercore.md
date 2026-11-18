---
title: "PB-03 PRT gebruiken via BrowserCore"
nav_order: 3
parent: "Playbooks"
---

# PB-03 PRT gebruiken via BrowserCore

| | |
|---|---|
| **Pad** | B, sub-aanpak 1 |
| **Doel** | Een bruikbaar PRT-token laten uitgeven zonder de sleutels te exporteren |
| **Tooling** | AADInternals v0.9.8 |
| **Waar** | Victim, PowerShell 7 als beheerder |
| **Status** | Getest — geslaagd |
| **Verdediging** | [PB-05]({{ '/playbooks/pb-05-token-protection/' | relative_url }}) |

**Startcondities.** Victim-VM, Entra-joined, vTPM, WHFB-PIN, volledig door Intune beheerd; aangemeld als `28_tu_whbf` met lokale adminrechten; PowerShell 7 als beheerder; AADInternals v0.9.8. Datum: 12/11/2026.

```
$prtToken = Get-AADIntUserPRTToken -Method BrowserCore
Get-AADIntAccessTokenForEXO -PRTToken $prtToken -SaveToCache
Get-AADIntAccessTokenForIntuneMDM -PRTToken $prtToken -SaveToCache
```

**Resultaat.** Een PRT-token als volledige JWT, en daarmee een access-token voor Exchange Online (resource `https://outlook.office365.com`, gebruiker `28_tu_whbf@aplab.be`) en voor Intune MDM, zonder MFA te herhalen.

**Waarom het slaagt.** BrowserCore is het onderdeel waarmee browsers op een Entra-joined toestel SSO krijgen. AADInternals vraagt langs die weg aan het TokenBroker-mechanisme van Windows om een PRT-token. De TPM tekent de aanvraag zelf; de privésleutel verlaat de TPM niet, maar het resultaat is wel bruikbaar. De verdedigingslagen beschermen de sleutel, niet het gebruik ervan door code die in de sessie van de gebruiker draait.

**Wat niet lukte.** `Get-AADIntAccessTokenForMSGraph -PRTToken` en `Get-AADIntAccessTokenForAADGraph -PRTToken` gaven "Code not received". Dat is een redirect-incompatibiliteit tussen AADInternals v0.9.8 en PowerShell 7. Microsoft Graph via BrowserCore is daardoor niet gedemonstreerd. Theoretisch moet het werken: hetzelfde PRT-token is geldig voor elke resource.
