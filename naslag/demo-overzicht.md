---
title: "Demo-overzicht"
nav_order: 2
parent: "Naslag"
---

# Demo-overzicht

| # | Demo | Pad | Startcondities | Tooling | Resultaat | Bewijs |
|---|---|---|---|---|---|---|
| 1 | Toestelregistratie en PRT met alleen een wachtwoord | B | Geen verdedigingspolicies | roadtx | Geslaagd | 5.3 |
| 2 | PRT-sleutels exporteren op beheerd toestel | B | vTPM, Intune, Defender | AADInternals | Geblokkeerd | 5.4.1 |
| 3 | PRT gebruiken via BrowserCore | B | Lokale beheerder op de victim | AADInternals | Geslaagd | 5.4.2 |
| 4 | Refresh-token replay | B | Geen verdedigingspolicies | roadtx | Geslaagd | 5.5 |
| 5 | Token Protection tegen replay en PRT | B | `28-CA-TokenProtection` | roadtx | Deels geblokkeerd | 8.2 |
| 6 | Platform-omzeiling en tegenmaatregel | B | Beide policies | roadtx | Omzeiling gesloten | 8.3 |
| 7 | BrowserCore met Token Protection | B | `28-CA-TokenProtection` | AADInternals | Geblokkeerd | 8.2 |
| 8 | MFA bij toestelregistratie | B | `28-CA-DeviceRegMFA` | roadtx | Geblokkeerd | 8.4 |
| 9 | TPM-lockout en smart lockout | B | WHFB-policy, vTPM | `Get-Tpm` | Meting voltooid | 7 |
| 10 | ESC1: vervalst certificaat als MFA | D | Kwetsbare template | Certipy | Geslaagd | 4.1 |
| 11 | AiTM tegen SMS | A | Aan te vullen | Evilginx3 | Aan te vullen | 6.3 |
| 12 | AiTM tegen software-OTP | A | Aan te vullen | Evilginx3 | Aan te vullen | 6.4 |
| 13 | AiTM tegen hardware-OTP | A | Aan te vullen | Evilginx3 | Aan te vullen | 6.5 |
| 14 | AiTM tegen push | A | Aan te vullen | Evilginx3 | Aan te vullen | 6.6 |
| 15 | Passkey in Authenticator: AiTM en proximity | C | Aan te vullen | python-fido2, Wireshark | Aan te vullen | 6.9 |
| 16 | Hardware dongle: AiTM en relaying | C | Aan te vullen | python-fido2, Wireshark | Aan te vullen | 6.10 |
| 17 | Tenbeo | C | Aan te vullen | Aan te vullen | Aan te vullen | 6.11 |

{: .todo }
> De opdracht vraagt per MFA-methode een schermopname of live-demo. Van de demo's van Pad B bestaan alleen screenshots; schermopnames moeten nog gemaakt worden. Vul per demo de bestandsnaam van de video in.
