---
title: "Tenant en policies"
nav_order: 2
parent: "Lab"
---

# Tenant en policies

## 3.4 Configuratie van de tenant

De volgende instellingen zijn in Entra ID en Intune gezet. Ze zijn in deze volgorde nodig om de victim op te bouwen.

| Instelling | Pad in het beheercentrum | Waarde en reden |
|---|---|---|
| MDM-gebruikersbereik | Intune | Stond al op "All"; niet gewijzigd wegens de gedeelde tenant |
| Temporary Access Pass | Entra → Protection → Authentication methods | Ingeschakeld; eenmalige MFA-bootstrap voor de eerste WHFB-inschrijving |
| Lokale adminrechten | Entra → Devices → Device settings → Local administrator settings | Rol "Microsoft Entra Joined Device Local Administrator" voor `28_tu_whbf`; werkt pas na volledig af- en aanmelden |
| WHFB-policy | Intune → Endpoint security → Account protection | WHFB ingeschakeld, TPM vereist, PIN minimaal 4 en maximaal 127; toegewezen aan `28_grp_padB` |
| Defender-exclusies | Intune → Endpoint security → Antivirus | Vier paden (zie hieronder); toegewezen aan `28_grp_padB` |

De Defender-exclusies waren nodig omdat AADInternals-Endpoints helper-programma's met een willekeurige naam aanmaakt, en omdat Tamper Protection lokale aanpassingen aan Defender blokkeert, ook voor een lokale beheerder.

| Uitgesloten pad | Reden |
|---|---|
| `C:\Program Files\PowerShell\Modules\AADInternals-Endpoints` | Modulemap van PowerShell 7 |
| `C:\Program Files\WindowsPowerShell\Modules\AADInternals-Endpoints` | Modulemap van PowerShell 5.1 |
| `C:\Users\28_tu_whbf\AppData\Local\Temp` | Tijdelijke helper-programma's van de gebruiker |
| `C:\Windows\Temp` | Systeembrede tijdelijke bestanden |

## 3.5 Conditional Access-policies voor de verdedigingstests

Deze drie policies zijn aangemaakt voor hoofdstuk 8. Ze zijn alleen op de eigen testaccounts gescopeerd.

| Policy | Gebruikers | Doel (target) | Voorwaarden | Controle |
|---|---|---|---|---|
| `28-CA-TokenProtection` | `28_tu_token`, `28_tu_whbf` | Exchange Online, SharePoint Online | Platform Windows; client apps "Mobile apps and desktop clients" | Session: Require token protection for sign-in sessions |
| `28-CA-BlockUnknownPlatform` | `28_tu_token` | Exchange Online | Platform: alle behalve Windows | Grant: Block access |
| `28-CA-DeviceRegMFA` | `28_tu_token` | User action: Register or join devices | — | Grant: Require multifactor authentication |
