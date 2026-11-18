---
title: "Scorekaart"
nav_order: 1
parent: "Resultaten"
---

# Scorekaart

{: .todo }
> De scores voor de methoden van Pad A en Pad C zijn voorlopig en steunen op analyse.

## 6.2 Het scoremodel

Elke methode krijgt een score op 10, opgebouwd uit de vier criteria die de opdracht noemt. Phishing-resistance weegt het zwaarst, omdat AiTM de meest gebruikte aanval is.

| Criterium | Maximum | Wat we beoordelen |
|---|---|---|
| Phishing-resistance | 4 | Houdt de methode een proxy of nagemaakte site tegen? |
| Binding | 3 | Is de factor aan een toestel of domein gebonden, en is de sleutel exporteerbaar? |
| Tokenrisico | 2 | Hoe bruikbaar is het token na de aanmelding voor een aanvaller? |
| Quantumbestendigheid | 1 | Blijft de onderliggende cryptografie veilig na Q-day? |

De scores gelden voor de standaardconfiguratie zonder de verdedigingen uit hoofdstuk 8. Het tokenrisico is voor bijna alle methoden gelijk: na de aanmelding ontstaat een sessie die zonder Token Protection niet aan het toestel gebonden is.

{: .todo }
> De scores voor de methoden van Pad A en Pad C zijn voorlopig. Ze steunen op analyse en moeten bevestigd of bijgesteld worden zodra de demo's zijn uitgevoerd.

De scores volgen het model uit sectie 6.2: phishing-resistance (4), binding (3), tokenrisico (2) en quantum (1). SAML, OAuth 2.0 en OIDC bieden zelf geen phishing-resistance; de factor bepaalt de score. Voor die drie protocollen geldt daarom dezelfde score per MFA-methode. CBA is zelf de factor en staat als aparte rij.

## 10.1 Scorekaart per MFA-methode (over SAML, OAuth 2.0 en OIDC)

| MFA-methode | Primaire bevinding | Aanval geblokkeerd? | Quantum | Score | Onderbouwing | Status |
|---|---|---|---|---|---|---|
| SMS | Code is door te sturen; SIM-swap mogelijk | Nee | N.v.t. | **2** | 6.3 | Voorlopig |
| Software-OTP | Code is door te sturen; seed kopieerbaar | Nee | Ja | **3** | 6.4 | Voorlopig |
| Hardware-OTP | Code is door te sturen; seed beschermd | Nee | Ja | **3,5** | 6.5 | Voorlopig |
| Authenticator-push | Number matching stopt fatigue, geen proxy | Nee | Gedeeltelijk | **4** | 6.6 | Voorlopig |
| Passwordless phone sign-in | Geen wachtwoord, wel door te sturen | Nee | Nee | **4,5** | 6.7 | Voorlopig |
| WHFB | Sleutels niet te exporteren; token wel te gebruiken via BrowserCore | Deels: extractie ja, gebruik pas met Token Protection | Nee | **8** | 6.8, 5.4, 7 | Getest |
| Passkey in Authenticator | Domain binding stopt AiTM | Ja (te bevestigen) | Nee | **7,5** | 6.9 | Voorlopig |
| Hardware dongle | Domain binding en fysieke aanwezigheid | Ja (te bevestigen) | Nee | **8** | 6.10 | Voorlopig |
| Tenbeo | Werking niet bevestigd | Onbekend | Onbekend | **—** | 6.11 | Aan te vullen |

## 10.2 Scorekaart voor certificate-based authentication

| Configuratie | Primaire bevinding | Aanval geblokkeerd? | Quantum | Score | Onderbouwing | Status |
|---|---|---|---|---|---|---|
| CBA met correct beheerde PKI | Geen deelbaar geheim; sterkte hangt af van sleutelopslag | Ja | Nee | **7** | 4.1 | Analyse |
| CBA met kwetsbare template (ESC1) | Vervalst certificaat aanvaard als MFA | Nee | Nee | **3** | 4.1 | Getest |

Opbouw: correct beheerd 4 + 2 + 1 + 0; met ESC1 zakt phishing-resistance naar 2 en binding naar 0, omdat iedereen met inschrijfrechten een geldig certificaat op andermans naam krijgt.

## 10.3 Opbouw van de scores

| Methode | Phishing-resistance (4) | Binding (3) | Tokenrisico (2) | Quantum (1) | Totaal |
|---|---|---|---|---|---|
| SMS | 0 | 0 | 1 | 1 | 2 |
| Software-OTP | 0,5 | 0,5 | 1 | 1 | 3 |
| Hardware-OTP | 0,5 | 1 | 1 | 1 | 3,5 |
| Authenticator-push | 1 | 1 | 1 | 1 | 4 |
| Passwordless phone sign-in | 1,5 | 1,5 | 1 | 0,5 | 4,5 |
| WHFB | 4 | 3 | 1 | 0 | 8 |
| Passkey in Authenticator | 4 | 2,5 | 1 | 0 | 7,5 |
| Hardware dongle | 4 | 3 | 1 | 0 | 8 |
| CBA (correct beheerd) | 4 | 2 | 1 | 0 | 7 |

## 10.4 Wat de scorekaart toont

- **Er is een scherpe grens tussen 4,5 en 7,5.** Aan de ene kant staan de methoden met een deelbaar geheim of een goedkeuring, aan de andere kant de methoden met een domeingebonden sleutel.
- **Geen enkele methode haalt 10.** De sterkste methoden verliezen een punt op tokenrisico, omdat de sessie na de aanmelding zonder extra policy niet gebonden is, en een punt op quantum.
- **Configuratie weegt zwaarder dan de methode.** CBA zakt van 7 naar 3 door één fout in een template. WHFB zou stijgen met Token Protection en zonder wachtwoord-terugval.
