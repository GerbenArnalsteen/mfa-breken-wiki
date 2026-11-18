---
title: "Opdracht en scope"
nav_order: 1
parent: "Project"
---

# Opdracht en scope

## 2.1 Achtergrond

In een zero-trustomgeving beslist de aanmelding over alle toegang. Aanvallers richten zich daarom op de identiteit in plaats van op het netwerk. Groepen als Lapsus$, Midnight Blizzard en Scattered Spider gebruiken drie technieken die ook werken wanneer MFA aanstaat:

- **Tokens stelen.** Na de aanmelding blijft een token achter dat de aanvaller kan meenemen.
- **MFA omzeilen.** De extra factor wordt via een proxy doorgestuurd of volledig overgeslagen.
- **OAuth-applicaties misbruiken.** Een kwaadaardige of fout geconfigureerde app krijgt blijvende toegang.

Het kerninzicht van dit rapport is dat een geslaagde aanmelding tokens oplevert: een Primary Refresh Token, een refresh-token en een access-token. Wie een van die tokens bezit, heeft de sessie. De sterkte van de MFA-methode speelt dan geen rol meer, tenzij het token aan het toestel gebonden is.

## 2.2 Opdracht en onderzoeksvragen

De opdracht omvat zes onderdelen. De tabel toont waar elk onderdeel in dit rapport staat.

| Onderdeel uit de opdracht | Plaats in dit rapport |
|---|---|
| Vier authenticatieprotocollen: CBA, SAML, OAuth 2.0, OIDC | Hoofdstuk 4 |
| Tokendiefstal en -misbruik: PRT, refresh-token, access-token | Hoofdstuk 5 |
| Negen MFA-methoden, elk met een workaround en demo | Hoofdstuk 6 |
| Onderzoeksvraag WHFB: PIN van 4 tegenover wachtwoord van 12 | Hoofdstuk 7 |
| Verdediging tegen tokendiefstal (token binding en andere) | Hoofdstuk 8 |
| Quantumbestendigheid per methode | Hoofdstuk 9 |
| Security score op 10 per combinatie, met motivatie | Hoofdstuk 10 |
| Lab-documentatie | Hoofdstuk 3 en bijlagen A en B |

## Op te leveren

| Deliverable | Inhoud |
|---|---|
| Technisch eindrapport | Per onderdeel: theorie, technische uitwerking, demo en bevindingen, quantumanalyse, score met motivatie |
| Demo's | Per MFA-methode een schermopname of live-demo: startcondities, stappen, resultaat |
| Security scorekaart | Per combinatie: primaire bevinding, geblokkeerd ja/nee, quantum, score op 10 |
| Lab-documentatie | Diagram, stapsgewijze installatie, tenant- en toolingconfiguratie |
| Presentatie | Maximaal 30 minuten plus 15 minuten vragen; elk groepslid presenteert |

## 2.4 Scope en ethisch kader

Alle aanvallen zijn uitgevoerd binnen de door de school geleverde Entra ID-tenant (`aplab.be`), uitsluitend op eigen testaccounts met de groepsprefix `28`. De lector bevestigde dat aanvallen op de eigen accounts binnen die tenant toegestaan zijn. De tenant wordt gedeeld met andere teams. Daaruit volgen twee werkregels die het hele project bepaalden:

- Tenant-brede instellingen zijn niet gewijzigd. Elke policy is gescopeerd op eigen testgebruikers of op de eigen toestelgroep (`28_grp_padB`).
- De phishing-proxy is niet publiek blootgesteld en er zijn geen accounts van derden benaderd.
