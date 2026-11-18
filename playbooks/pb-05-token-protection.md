---
title: "PB-05 Token Protection"
nav_order: 5
parent: "Playbooks"
---

# PB-05 Token Protection

| | |
|---|---|
| **Pad** | B, verdediging |
| **Doel** | Nagaan welke tokenaanvallen Token Protection tegenhoudt |
| **Policy** | `28-CA-TokenProtection` |
| **Tooling** | roadtx, AADInternals |
| **Status** | Getest — deels geblokkeerd |

**Theorie.** Token Protection is een session control in Conditional Access. De policy eist dat het token dat een applicatie aanbiedt, cryptografisch gebonden is aan een geregistreerd toestel. Een gestolen token dat vanaf een ander toestel wordt aangeboden, voldoet daar niet aan. De controle geldt alleen voor de resources, platformen en client-apps in de policy; in onze opzet Exchange Online en SharePoint Online, op Windows, voor desktop- en mobiele apps.

**Startcondities.** Policy `28-CA-TokenProtection` (sectie 3.5). Aanvaller op de Attacker Windows-VM met het wachtwoord van `28_tu_token` en een PRT voor het zelf geregistreerde `attackerdevice`. Datum: 17/11/2026.

```
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r https://outlook.office365.com
roadtx refreshtokento -r https://outlook.office365.com
roadtx prtauth -r https://outlook.office365.com

# Run 3 en 4: dezelfde commando's met een Windows-user-agent
$ua = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36 Edg/130.0.0.0'
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r https://outlook.office365.com -ua $ua
```

| Run | Policy | User-agent | Wachtwoord-aanmelding | Refresh-token replay | PRT van aanvallerstoestel |
|---|---|---|---|---|---|
| 1 | Report-only | Standaard van roadtx | Geslaagd | Geslaagd | Geslaagd |
| 2 | On | Standaard van roadtx | Geslaagd | Geslaagd | Geslaagd |
| 3 | On | Windows | **Geblokkeerd** (AADSTS530084) | **Geblokkeerd** (AADSTS530084) | Geslaagd |

![Figuur 8.1 — Run 1: in report-only zou de policy de aanvragen doorlaten]({{ '/assets/img/image7.png' | relative_url }})

*Figuur 8.1 — Run 1: in report-only zou de policy de aanvragen doorlaten*

![Figuur 8.2 — Run 2: het besturingssysteem is leeg, de policy wordt niet toegepast]({{ '/assets/img/image8.png' | relative_url }})

*Figuur 8.2 — Run 2: het besturingssysteem is leeg, de policy wordt niet toegepast*

![Figuur 8.3 — Run 3: twee aanvragen geblokkeerd, de PRT-aanvraag slaagt]({{ '/assets/img/image9.png' | relative_url }})

*Figuur 8.3 — Run 3: twee aanvragen geblokkeerd, de PRT-aanvraag slaagt*

![Figuur 8.4 — Run 3: de policy geeft Failure voor de wachtwoord-aanmelding]({{ '/assets/img/image10.png' | relative_url }})

*Figuur 8.4 — Run 3: de policy geeft Failure voor de wachtwoord-aanmelding*

![Figuur 8.5 — Run 3: de policy geeft Failure voor de refresh-token replay]({{ '/assets/img/image11.png' | relative_url }})

*Figuur 8.5 — Run 3: de policy geeft Failure voor de refresh-token replay*

**Bevinding 1: de policy is te omzeilen via het platform.** Met de standaard user-agent van roadtx bleef het veld "Operating system" in het aanmeldlogboek leeg. Entra ID herkende de aanvraag niet als Windows, paste de policy niet toe en liet alles door. Een Token Protection-policy die alleen op Windows gescopeerd is, beschermt dus niet tegen een aanvaller die zich niet als Windows meldt.

**Bevinding 2: Token Protection blokkeert ongebonden tokens.** Zodra de aanvraag als Windows herkend werd, zijn de wachtwoord-aanmelding en de refresh-token replay geweigerd. Het aanmeldlogboek toont voor beide `28-CA-TokenProtection` met resultaat Failure; de policies van andere teams staan op "Not applied", dus de blokkade komt alleen van onze policy.

**Bevinding 3: de PRT van een door de aanvaller geregistreerd toestel wordt niet geblokkeerd.** `prtauth` bleef slagen. Het verkregen access-token bevat `aud` = `https://outlook.office365.com`, `amr` = `pwd`, `rsa`, en als `deviceid` het ID van `attackerdevice` (vergeleken met Entra admin center). Het is uitgegeven nadat de policy was aangezet.

**BrowserCore op de victim (run 5).** Met de policy actief herhaalden we de aanval uit sectie 5.4.2. Het PRT-token ophalen lukte nog, maar het inwisselen voor een Exchange Online-token werd geweigerd met fout 530084. Token Protection stopt deze aanval dus voor de beschermde resources.

![Figuur 8.6 — Run 5: de BrowserCore-aanval wordt geblokkeerd door Token Protection]({{ '/assets/img/image13.png' | relative_url }})

*Figuur 8.6 — Run 5: de BrowserCore-aanval wordt geblokkeerd door Token Protection*

Het contrast is opvallend: de PRT van het echte, beheerde toestel wordt via BrowserCore geblokkeerd, terwijl de PRT van het aanvallerstoestel via roadtx doorgelaten wordt. Onze vermoedelijke verklaring, die we niet konden verifiëren: BrowserCore levert een PRT-cookie voor een browser-aanmelding, wat geen toestelgebonden sessie oplevert, terwijl roadtx de aanvraag rechtstreeks ondertekent met de sessiesleutel van de PRT.
