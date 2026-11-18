---
title: "Aanpak en proces"
nav_order: 2
parent: "Project"
---

# Aanpak en proces

## 2.3 Aanpak: vier aanvalspaden

De opdracht somt dertien losse items op. Methoden met dezelfde zwakte delen echter één aanvalstechniek. We groepeerden het werk daarom in vier paden, elk met één eigenaar in het team.

| Pad | Wat het test | Opstelling | Hoofdtooling | Kernvraag |
|---|---|---|---|---|
| A — AiTM via de browser | SMS, software-OTP, hardware-OTP, push; SAML/OIDC/OAuth als transport | Victim → proxy → tenant | Evilginx3 | Kan een deelbaar geheim of goedkeuring onderschept worden? |
| B — Tokendiefstal en WHFB | PRT, refresh- en access-token; PIN tegenover wachtwoord | Attacker Windows en victim met vTPM | roadtx, AADInternals | Wat kan een aanvaller met een token, en stopt binding dat? |
| C — Proximity bij passkeys | Passkey in Authenticator, hardware dongle, Tenbeo | Fysieke toestellen op de host | python-fido2, Wireshark | Houdt de proximity-check stand of is relaying mogelijk? |
| D — PKI en CBA | Certificate-based authentication | DC met AD CS en Kali | Certipy | Is de certificaatketen zelf het zwakke punt? |

Elk onderdeel volgt hetzelfde stramien: werking, aanvalsoppervlak, uitgevoerde aanval en bevinding, verdediging, quantum. Dat zijn ook de criteria van de scorekaart.

## Werkwijze in vier stappen

| Stap | Wat | Resultaat |
|---|---|---|
| 1. Basisanalyse | Elk protocol en elke methode op dezelfde manier ontleden | [Theorie]({{ '/theorie/' | relative_url }}) |
| 2. Klantvragen | Gericht vragen naar diepgang en prioriteiten | Scope per pad |
| 3. Labontwerp | Eén gedeelde tenant, een eigen opstelling per pad | [Lab]({{ '/lab/' | relative_url }}) |
| 4. Aanvallen en demo's | Uitvoeren, vastleggen, verdedigen en scoren | [Playbooks]({{ '/playbooks/' | relative_url }}), [Resultaten]({{ '/resultaten/' | relative_url }}) |

## Hoe een test verloopt

1. **Startcondities vastleggen:** welke policies staan aan, welk account, welke tool en versie.
2. **Baseline:** de aanval uitvoeren zonder verdediging en het resultaat noteren.
3. **Verdediging aanzetten:** eerst in report-only, het aanmeldlogboek controleren, dan op On.
4. **Aanval herhalen:** per commando de uitvoer en de regel in het aanmeldlogboek vastleggen.
5. **Documenteren:** resultaat, verklaring, wat niet verklaard is, en het bewijs, in het [playbook]({{ '/playbooks/' | relative_url }}).

## Statuslabels

| Label | Betekenis |
|---|---|
| **Getest** | Zelf uitgevoerd in het lab; resultaat en bewijs staan op de pagina. |
| **Analyse** | Onderbouwd met theorie en bronnen, niet zelf uitgevoerd. |
| **Aan te vullen** | Het labwerk loopt nog of het resultaat is nog niet verwerkt. Deze plaatsen zijn geel gemarkeerd. |

## Regels in de gedeelde tenant

- Wijzig geen tenant-brede instellingen.
- Scope elke policy op eigen testgebruikers of de eigen toestelgroep.
- Gebruik alleen accounts met de eigen groepsprefix.
- Maak een snapshot vóór elke destructieve test.
- Ruim policies en testtoestellen op na afloop.
