---
title: "Pad C — Proximity bij passkeys"
nav_order: 3
parent: "De vier paden"
---

# Pad C — Proximity bij passkeys

| | |
|---|---|
| **Kernvraag** | Houdt de proximity-check stand, of is relaying mogelijk? |
| **Dekt** | Passkey in Microsoft Authenticator, passkey met hardware dongle, Tenbeo |
| **Opstelling** | Fysieke toestellen op de host (geen VM): YubiKey, smartphone; python-fido2 en Wireshark |
| **Status** | Aan te vullen |

## Wat dit pad onderzoekt

Passkeys binden de privésleutel aan het echte domein. Een proxy met een nagemaakte pagina krijgt geen geldige assertion. Dit pad onderzoekt een andere vector: kan de fysieke aanwezigheid van de sleutel of het toestel geveinsd of doorgestuurd worden?

| Sub-scenario | Technologie | Vraag |
|---|---|---|
| Passkey in Authenticator, cross-device | QR-code en Bluetooth Low Energy (hybride transport) | Is de proximity-check af te dwingen, of is de handshake door te sturen? |
| Hardware dongle | USB en NFC | Is het signaal van de sleutel te relayen naar een lezer op afstand? |
| Tenbeo | Hartslag- of ECG-biometrie | Is het een echte FIDO2-passkey of biometrische matching? |

## Waarom geen VM

USB- en NFC-doorgifte naar een gast-VM werkt niet betrouwbaar voor FIDO2. De tests draaien daarom op de host.

## Methodologische waarschuwing

In 2025 claimde Expel een geslaagde relay in de PoisonSeed-campagne en trok die claim later in: de proximity-check had wel standgehouden. Een proxy kan een QR-code doorsturen zonder dat de Bluetooth-controle omzeild is. Bouw de test zo dat dit onderscheid hard is: plaats de toestellen bewust ver uit elkaar of afgeschermd, en meet of de aanmelding dan nog slaagt.

## Resultaten

{: .todo }
> Door de eigenaar van dit pad: per aanval de startcondities, de stappen, het resultaat (gelukt of mislukt) en het bewijs. Gebruik het [playbook-sjabloon]({{ '/playbooks/sjabloon/' | relative_url }}).

Theorie per methode: [MFA-methoden]({{ '/theorie/mfa-methoden/' | relative_url }}).
