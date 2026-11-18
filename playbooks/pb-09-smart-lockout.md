---
title: "PB-09 Smart lockout van het wachtwoord"
nav_order: 9
parent: "Playbooks"
---

# PB-09 Smart lockout van het wachtwoord

| | |
|---|---|
| **Pad** | B, onderzoeksvraag |
| **Doel** | Meten wanneer Entra ID een account vergrendelt bij foute wachtwoorden |
| **Tooling** | Lockscherm van de victim, browser |
| **Status** | Getest; duur van de blokkade aan te vullen |

## Stappen

1. Voer op het lockscherm van de victim foute wachtwoorden in, telkens een ander.
2. Tel de pogingen en noteer wanneer het gedrag verandert.
3. Probeer daarna aan te melden in een browser en noteer de melding.
4. Noteer na hoeveel tijd het juiste wachtwoord weer werkt.

Op het lockscherm van dezelfde victim-VM voerden we foute wachtwoorden in voor `28_tu_whbf`.

| Aantal foute wachtwoorden | Wat er gebeurde |
|---|---|
| Na 5 | Windows keerde na elke volgende foute poging terug naar het lockscherm |
| Na 10 | Het account was vergrendeld |
| Direct daarna, in de browser | "Uw account is tijdelijk vergrendeld om niet-geautoriseerd gebruik te voorkomen." |

![Figuur 7.6 — Het account is in de cloud vergrendeld na foute wachtwoorden op het lockscherm]({{ '/assets/img/lockout.png' | relative_url }})

*Figuur 7.6 — Het account is in de cloud vergrendeld na foute wachtwoorden op het lockscherm*

De pogingen op het lockscherm van een Entra-joined toestel worden dus door Entra ID geteld, en de blokkade geldt voor het account zelf. De drempel van 10 komt overeen met de standaard van Entra smart lockout.

{: .todo }
> De gemeten duur van de wachtwoordblokkade.
