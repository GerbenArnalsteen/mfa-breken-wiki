---
title: "Playbooks"
nav_order: 6
has_children: true
---

# Playbooks

Eén pagina per aanval of test, stap voor stap uitvoerbaar. Elk playbook heeft dezelfde opbouw: startcondities, uitvoering, resultaat, verklaring en bewijs.

| # | Playbook | Pad | Soort | Resultaat |
|---|---|---|---|---|
| PB-01 | [Toestelregistratie en PRT]({{ '/playbooks/pb-01-toestelregistratie/' | relative_url }}) | B | Aanval | Geslaagd |
| PB-02 | [PRT-sleutels exporteren]({{ '/playbooks/pb-02-prt-extractie/' | relative_url }}) | B | Aanval | Geblokkeerd |
| PB-03 | [PRT gebruiken via BrowserCore]({{ '/playbooks/pb-03-browsercore/' | relative_url }}) | B | Aanval | Geslaagd |
| PB-04 | [Refresh-token replay]({{ '/playbooks/pb-04-refresh-token-replay/' | relative_url }}) | B | Aanval | Geslaagd |
| PB-05 | [Token Protection]({{ '/playbooks/pb-05-token-protection/' | relative_url }}) | B | Verdediging | Deels geblokkeerd |
| PB-06 | [Onbekende platformen blokkeren]({{ '/playbooks/pb-06-platform-policy/' | relative_url }}) | B | Verdediging | Omzeiling gesloten |
| PB-07 | [MFA bij toestelregistratie]({{ '/playbooks/pb-07-mfa-bij-registratie/' | relative_url }}) | B | Verdediging | Geblokkeerd |
| PB-08 | [TPM-lockout van de WHFB-PIN]({{ '/playbooks/pb-08-tpm-lockout/' | relative_url }}) | B | Meting | Voltooid |
| PB-09 | [Smart lockout van het wachtwoord]({{ '/playbooks/pb-09-smart-lockout/' | relative_url }}) | B | Meting | Voltooid |
| PB-10 | [ESC1 op CBA]({{ '/playbooks/pb-10-esc1/' | relative_url }}) | D | Aanval | Geslaagd; aan te vullen |
| — | [Sjabloon voor een nieuw playbook]({{ '/playbooks/sjabloon/' | relative_url }}) | | | |

{: .todo }
> Playbooks voor Pad A (AiTM tegen SMS, software-OTP, hardware-OTP en push) en Pad C (passkey in Authenticator, hardware dongle, Tenbeo) moeten nog toegevoegd worden.
