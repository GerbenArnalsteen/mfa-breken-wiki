---
title: "Verdediging"
nav_order: 7
---

# Verdediging

| Verdediging | Wat ze doet | Getest? |
|---|---|---|
| TPM-binding van sleutels | Privésleutels zijn niet exporteerbaar | Ja (sectie 5.4.1) |
| Defender, Tamper Protection, Intune | Verwijderen aanvalstooling en vergrendelen instellingen | Ja (sectie 5.4.1) |
| Token Protection (Conditional Access) | Vereist dat het token aan een toestel gebonden is | Ja (sectie 8.2) |
| Onbekende platformen blokkeren | Sluit de omzeiling van platformgebonden policies | Ja (sectie 8.3) |
| MFA bij toestelregistratie | Voorkomt registratie met alleen een wachtwoord | Ja (sectie 8.4) |
| Phishing-resistente MFA afdwingen | Stopt AiTM bij de aanmelding | Aan te vullen (Pad A) |
| Compliant device vereisen | Laat alleen beheerde toestellen toe | Niet getest; analyse |

De uitvoering per verdediging staat in de playbooks: [PB-05 Token Protection]({{ '/playbooks/pb-05-token-protection/' | relative_url }}), [PB-06 Onbekende platformen blokkeren]({{ '/playbooks/pb-06-platform-policy/' | relative_url }}) en [PB-07 MFA bij toestelregistratie]({{ '/playbooks/pb-07-mfa-bij-registratie/' | relative_url }}).

## 8.5 Wat stopt welke aanval?

| Aanval | Zonder verdediging | Token Protection | + onbekende platformen blokkeren | + MFA bij registratie |
|---|---|---|---|---|
| Token ophalen met alleen een wachtwoord | Geslaagd | Geblokkeerd als Windows; omzeilbaar | Geblokkeerd | Geblokkeerd |
| Refresh-token replay | Geslaagd | Geblokkeerd als Windows; omzeilbaar | Geblokkeerd | Geblokkeerd |
| Toestel registreren en PRT aanvragen | Geslaagd | Niet geblokkeerd | Niet geblokkeerd | **Geblokkeerd** (behalve met een oud token) |
| PRT van aanvallerstoestel gebruiken | Geslaagd | Niet geblokkeerd | Niet geblokkeerd | N.v.t.: toestel bestaat niet |
| BrowserCore op de victim | Geslaagd | **Geblokkeerd** | Geblokkeerd | N.v.t. |
| PRT-sleutels exporteren | Geblokkeerd door TPM | — | — | — |

Alle cellen in deze tabel zijn getest. Geen enkele verdediging stopt alles; pas de combinatie sluit de keten.

## 8.6 Verdedigingen per MFA-methode

De opdracht vraagt per MFA-methode welke aanvallen de verdedigingen blokkeren. De tokenaanvallen uit dit hoofdstuk staan los van de gebruikte MFA-methode: ze beginnen bij een wachtwoord of bij een bestaande sessie. De tabel combineert onze geteste resultaten met de analyse voor de AiTM-aanval.

| MFA-methode | AiTM bij de aanmelding | Sessie of token na de aanmelding | Wat blokkeert | Status |
|---|---|---|---|---|
| SMS, software-OTP, hardware-OTP, push | Slaagt | Te stelen via de proxy | Phishing-resistente MFA afdwingen; Token Protection voor ondersteunde apps | Aan te vullen (Pad A) |
| Passwordless phone sign-in | Slaagt | Te stelen via de proxy | Zoals hierboven | Aan te vullen |
| WHFB | Mislukt: geen deelbaar geheim | Bruikbaar token via BrowserCore | Token Protection blokkeert BrowserCore | **Getest** |
| Passkey in Authenticator, hardware dongle | Mislukt door domain binding | Browsersessie blijft ongebonden | Token Protection dekt browsersessies niet | Aan te vullen (Pad C) |
| CBA | Mislukt: geen deelbaar geheim | Vervalst certificaat telt als MFA | PKI-hardening (sectie 4.1) | Getest (Pad D) |

Eén beperking geldt voor alle methoden: Token Protection zoals wij het testten, dekt desktop- en mobiele apps op Windows voor een beperkt aantal resources. Een sessiecookie die een proxy uit een browser steelt, valt daarbuiten.

{: .todo }
> Door de eigenaar van Pad A: de AiTM-aanvallen herhaald met een policy die phishing-resistente MFA afdwingt, met het verschil per methode.
