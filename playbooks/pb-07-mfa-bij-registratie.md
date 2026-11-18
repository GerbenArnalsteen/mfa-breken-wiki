---
title: "PB-07 MFA bij toestelregistratie"
nav_order: 7
parent: "Playbooks"
---

# PB-07 MFA bij toestelregistratie

| | |
|---|---|
| **Pad** | B, verdediging |
| **Doel** | De toestelregistratie met alleen een wachtwoord stoppen |
| **Policy** | `28-CA-DeviceRegMFA` |
| **Tooling** | roadtx |
| **Status** | Getest — geblokkeerd |

**Theorie.** Entra ID kent een tenant-brede instelling "Require MFA to register or join devices" en een Conditional Access-variant via de user action "Register or join devices". Omdat de tenant gedeeld is, gebruikten we de tweede, gescopeerd op `28_tu_token`.

| Poging | Tijdstip (UTC) | Token voor registratie | Toestel registreren | Resultaat |
|---|---|---|---|---|
| 1 | 19:03 | Geslaagd | Geslaagd | `attackerdevice2` geregistreerd |
| 2 | 20:06 | **Geblokkeerd** (AADSTS50079) | Geslaagd | Tweede `attackerdevice2` geregistreerd |
| 3 | 20:10 | **Geblokkeerd** (AADSTS50079) | **Mislukt**: geen token | Geen toestel |

![Figuur 8.8 — Poging 3: zonder oud token stopt de keten bij de eerste stap]({{ '/assets/img/image14.png' | relative_url }})

*Figuur 8.8 — Poging 3: zonder oud token stopt de keten bij de eerste stap*

**Bevindingen.**

1. **Poging 1 slaagde volledig.** Het aanmeldlogboek toonde voor deze aanmelding alleen "Not applicable" op het tabblad Conditional Access. De meest waarschijnlijke oorzaak is dat de policy kort voordien was aangemaakt en nog niet actief was; het aanmaaktijdstip is niet genoteerd, dus dit is niet bewezen.
2. **Zodra de policy actief is, wordt de token-aanvraag geweigerd.** Een wachtwoord alleen volstaat niet meer.
3. **Een eerder verkregen token blijft bruikbaar.** In poging 2 lukte de registratie toch, met het token uit poging 1 dat nog op schijf stond. De MFA-eis geldt bij de uitgifte van het token, niet bij de registratie zelf.
4. **Zonder oud token stopt de keten volledig** (poging 3).

De device-limiet verlagen is niet getest: dat is een tenant-brede instelling.

## Commando's

```
Remove-Item .roadtools_auth
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r devicereg
roadtx device -a join -n attackerdevice3
```

Verwijder `.roadtools_auth` vóór de test: een ouder token maakt het resultaat ongeldig.
