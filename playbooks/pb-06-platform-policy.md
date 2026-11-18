---
title: "PB-06 Onbekende platformen blokkeren"
nav_order: 6
parent: "Playbooks"
---

# PB-06 Onbekende platformen blokkeren

| | |
|---|---|
| **Pad** | B, verdediging |
| **Doel** | De platform-omzeiling van Token Protection sluiten |
| **Policy** | `28-CA-BlockUnknownPlatform` naast `28-CA-TokenProtection` |
| **Tooling** | roadtx |
| **Status** | Getest |

Om de omzeiling uit bevinding 1 te sluiten, voegden we `28-CA-BlockUnknownPlatform` toe: blokkeer Exchange Online voor elk platform behalve Windows.

| Commando | User-agent | Resultaat |
|---|---|---|
| `roadtx gettokens` (wachtwoord) | Standaard | **Geblokkeerd** (AADSTS53003) |
| `roadtx refreshtokento` | Windows | **Geblokkeerd** (AADSTS530084) |
| `roadtx prtauth` | Standaard | Geslaagd |
| `roadtx prtauth` | Windows | Geslaagd |

![Figuur 8.7 — Run 4: beide policies actief; alleen de PRT-aanvraag slaagt nog]({{ '/assets/img/image12.png' | relative_url }})

*Figuur 8.7 — Run 4: beide policies actief; alleen de PRT-aanvraag slaagt nog*

De combinatie sluit de omzeiling voor ongebonden tokens. De PRT-route komt door beide policies, met en zonder Windows-user-agent. Het toestelrecord `attackerdevice` staat in Entra ID geregistreerd als Windows. De verklaring die daarbij past: bij een PRT-aanmelding kent Entra ID het toestel en bepaalt het platform uit het toestelrecord, niet uit de user-agent. De aanvraag valt daardoor buiten de platformblokkade en binnen Token Protection, die haar doorlaat omdat de PRT aan een geregistreerd toestel gebonden is.

{: .note }
> Voorbehoud. De regel van `prtauth` is niet teruggevonden in het aanmeldlogboek, ook niet via het unieke token-ID, en het token heeft een leeg `enfpolids`-veld. Dat Token Protection deze aanvraag als "voldaan" beoordeelde, is afgeleid uit het gedrag en niet rechtstreeks waargenomen.

**Conclusie.** Token Protection controleert of een token aan een toestel gebonden is, niet of dat toestel te vertrouwen is. De keten "toestel registreren, PRT aanvragen, token ophalen" moet dus bij de registratie gestopt worden, of met een eis dat het toestel compliant is.
