---
title: "Conclusies en aanbevelingen"
nav_order: 3
parent: "Resultaten"
---

# Conclusies en aanbevelingen

## 11.1 Conclusies

1. **MFA wordt omzeild, niet gekraakt.** In geen enkele geslaagde aanval is een MFA-factor gebroken. Telkens werd een token verkregen langs een weg waar MFA niet gevraagd werd of al voorbij was.
2. **Het wachtwoord is de zwakste schakel, ook naast sterke methoden.** Een wachtwoord volstond om een toestel te registreren en tokens te verkrijgen, en bleef werken terwijl de WHFB-PIN vergrendeld was.
3. **Binding werkt, maar beschermt precies wat ze bindt.** De TPM beschermt de sleutel, niet het gebruik ervan. Token Protection controleert de binding, niet de betrouwbaarheid van het toestel.
4. **Verdedigingen hebben voorwaarden, en aanvallers zoeken de rand op.** Een policy die alleen voor Windows geldt, is te ontwijken door geen Windows te zijn.
5. **De keten van vertrouwen reikt verder dan de identiteitsprovider.** Een fout in de PKI gaf een geldig MFA-bewijs voor een willekeurige gebruiker.

## 11.2 Aanbevelingen

| Prioriteit | Aanbeveling | Stopt | Onderbouwing |
|---|---|---|---|
| 1 | MFA vereisen voor het registreren of joinen van toestellen | Toestelregistratie met alleen een wachtwoord | 5.3, 8.4 |
| 1 | Phishing-resistente MFA afdwingen (Authentication Strengths); SMS en OTP uitfaseren | AiTM bij de aanmelding | 6.3 tot 6.6 |
| 2 | Token Protection inschakelen, samen met een policy die onbekende platformen blokkeert | Hergebruik van gestolen tokens en de platform-omzeiling | 8.2, 8.3 |
| 2 | Compliant of hybrid joined device vereisen voor gevoelige resources | PRT van een door de aanvaller geregistreerd toestel | 8.3 |
| 2 | Wachtwoord-aanmelding uitschakelen voor gebruikers met WHFB of een passkey | Het wachtwoord als zijdeur | 7 |
| 2 | Verouderde flows blokkeren (wachtwoord-flow, device-code-flow) | Tokens verkrijgen zonder interactieve aanmelding | 4.3 |
| 3 | Certificaattemplates en CA-rechten controleren op de ESC-misconfiguraties; sterke certificaatmapping | Vervalste certificaten als MFA | 4.1 |
| 3 | Lokale beheerdersrechten beperken op beheerde toestellen | BrowserCore-aanval | 5.4 |
| 3 | Een migratiepad naar post-quantum authenticatie opvolgen | Kwetsbaarheid na Q-day | 9 |

Bij het invoeren van policies in een Windows-omgeving: test eerst in report-only en controleer in het aanmeldlogboek of de policy op de bedoelde aanmeldingen wordt toegepast. In onze tests gaf report-only "Success" voor aanvragen waarop de policy na het aanzetten niet werd toegepast.
