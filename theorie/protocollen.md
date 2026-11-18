---
title: "Protocollen"
nav_order: 1
parent: "Theorie"
---

# Protocollen

De vier protocollen doen niet hetzelfde. CBA bewijst identiteit met een certificaat. SAML en OIDC regelen aanmelding bij applicaties. OAuth 2.0 regelt toegang voor applicaties. In Entra ID hangen ze samen: Entra is de identiteitsprovider, spreekt SAML of OIDC met applicaties, geeft onderliggend OAuth 2.0-tokens uit en aanvaardt CBA als sterke primaire factor.

| Protocol | Wat het doet | Belangrijkste zwakte | Phishing-resistent? |
|---|---|---|---|
| CBA | Identiteit bewijzen met een X.509-certificaat | De PKI erachter: wie mag certificaten uitgeven? | Ja, er is geen deelbaar geheim |
| SAML | Web-SSO met een ondertekende XML-assertion | Handtekeningvalidatie en Golden SAML | Nee, hangt af van de factor |
| OAuth 2.0 | Autorisatie: toegang voor een app, via tokens | Tokendiefstal en consent phishing | Nee, hangt af van de factor |
| OIDC | Authenticatie bovenop OAuth, via een ID-token | Zwakke tokenvalidatie; erft de OAuth-zwaktes | Nee, hangt af van de factor |

## 4.1 Certificate-based authentication (CBA)

**Werking.** De gebruiker of het toestel bezit een X.509-certificaat met een privésleutel, liefst opgeslagen in een TPM of smartcard. Bij het aanmelden bewijst de client het bezit van de privésleutel met een cryptografische challenge. De server controleert het certificaat tegen een vertrouwde certificeringsinstantie (CA). In Entra ID kan CBA als primaire, wachtwoordloze factor dienen, en afhankelijk van de configuratie telt het certificaat als één of als twee factoren.

**Aanvalsoppervlak.**

- *Zwakke of misbruikte PKI.* Wie te veel rechten heeft op de CA of op een certificaattemplate, kan certificaten laten uitgeven op naam van anderen. De ESC-reeks van SpecterOps beschrijft deze misconfiguraties in Active Directory Certificate Services.
- *Diefstal van de privésleutel.* Een sleutel die in software exporteerbaar is, kan gekopieerd en elders gebruikt worden.
- *Fouten in mapping en validatie.* Een te losse koppeling tussen certificaat en account, of ontbrekende intrekkingscontrole (CRL/OCSP).

**Uitgevoerde aanval: ESC1 (Pad D) — Getest.** De aanval misbruikt een certificaattemplate waarin "Supply in the request" aanstaat: de aanvrager kiest dan zelf op welke naam het certificaat wordt uitgegeven.

| Stap | Wat er gebeurt |
|---|---|
| 1 | Kwetsbare template in AD CS vinden: "Supply in the request" staat aan |
| 2 | Certificaat aanvragen op een zelfgekozen naam: voor de domeinbeheerder en voor een Entra-testgebruiker |
| 3 | Aanmelden met dat certificaat: on-prem met Certipy, in de cloud via de browser |

Resultaat: geslaagd op beide fronten.

- **On-prem:** aangemeld als Administrator via LDAPS op de domain controller.
- **Cloud:** de MFA-policy is omzeild. Entra ID aanvaardde het certificaat als MFA, zonder dat de Authenticator-app nodig was.

{: .todo }
> Door de eigenaar van Pad D: de exacte Certipy-commando's (`certipy find`, `certipy req`, `certipy auth`), de configuratie van de template en van Entra CBA (telt het certificaat als één of twee factoren, welke mapping), en de screenshots.

**Waarom de aanval slaagt.** Het protocol zelf is niet gebroken. Entra ID vertrouwt elk certificaat dat door de geconfigureerde CA is uitgegeven en dat op een account past. De vraag "wie is deze gebruiker" is daardoor verschoven naar "wie mag bij deze CA een certificaat op die naam krijgen". Een fout in één template volstaat om elke gebruiker te imiteren, inclusief het MFA-bewijs.

**Verdediging.** "Supply in the request" uitschakelen of manager-goedkeuring vereisen op templates met clientauthenticatie; inschrijfrechten beperken; sterke certificaatmapping in Entra CBA (op basis van uitgever en serienummer of SKI in plaats van alleen de naam); intrekkingscontrole afdwingen; de CA monitoren als een systeem van het hoogste niveau.

**Phishing-resistance.** Hoog: er is geen geheim dat een gebruiker in een nagemaakte pagina kan intikken. De zwakte zit in de keten die certificaten uitgeeft.

**Quantum.** Certificaten steunen op RSA of ECC. Beide zijn te breken met het algoritme van Shor zodra er een voldoende krachtige quantumcomputer bestaat. Zonder migratie naar post-quantum handtekeningen is CBA op termijn kwetsbaar (hoofdstuk 9).

## 4.2 SAML

**Werking.** SAML 2.0 regelt web-SSO tussen een identiteitsprovider (IdP) en een serviceprovider (SP). De gebruiker meldt aan bij de IdP. Die stuurt een digitaal ondertekende XML-assertion via de browser naar de SP, die de handtekening vertrouwt en de gebruiker aanmeldt.

**Aanvalsoppervlak.**

- *Zwakke handtekeningvalidatie.* Bij XML Signature Wrapping manipuleert de aanvaller de structuur van het document zodat de SP een niet-ondertekend deel aanvaardt.
- *Golden SAML.* Wie de ondertekeningssleutel van de IdP bemachtigt, maakt zelf geldige assertions voor elke gebruiker. Dat is krachtig en moeilijk te detecteren, want er is geen aanmelding bij de IdP meer nodig.
- *Slecht gevalideerde velden.* Ontbrekende controle op `Audience`, `Recipient`, tijdsvensters of hergebruik.
- *AiTM.* De flow loopt door de browser, dus een reverse proxy kan de sessie onderscheppen.

**Uitwerking — Analyse.** SAML is in dit project niet met een eigen test-SP gedemonstreerd. De AiTM-zwakte is dezelfde als bij OIDC en wordt via Pad A aangetoond. Golden SAML is theoretisch uitgewerkt: het vereist de ondertekeningssleutel, wat in een cloud-only Entra-tenant neerkomt op beheerdersrechten op de federatie-instellingen.

{: .todo }
> Te beslissen door het team: blijft SAML bij analyse (met OIDC als live-demo), of wordt er nog een test-SP gebouwd? Pas deze alinea aan volgens de beslissing en het antwoord van de opdrachtgever.

**Phishing-resistance.** Het protocol beschermt niet tegen een proxy. De weerstand komt van de factor die de IdP gebruikt.

**Quantum.** De handtekeningen steunen op RSA of ECDSA en zijn dus post-quantum kwetsbaar. SAML is bovendien een ouder protocol dat traag evolueert.

## 4.3 OAuth 2.0

**Werking.** OAuth 2.0 is een autorisatiestandaard (RFC 6749). Een applicatie krijgt namens de gebruiker toegang tot gegevens zonder het wachtwoord te kennen. Er zijn vier rollen: de resource owner (gebruiker), de client (applicatie), de authorization server (Entra ID) en de resource server (de API). De aanbevolen flow is de Authorization Code flow met PKCE: de applicatie krijgt via de browser een tijdelijke code en wisselt die op de achtergrond in voor een access-token en meestal een refresh-token. OAuth zegt niets over wie de gebruiker is; het regelt alleen wat een applicatie mag.

**Aanvalsoppervlak.** We onderscheiden vijf clusters.

| Aanval | Grijpt in bij | Omzeilt MFA? | Kernverdediging |
|---|---|---|---|
| Device code phishing | Start van de flow | Ja, op tokenniveau | Device-code-flow blokkeren via Conditional Access |
| AiTM (Evilginx) | Aanmelding en sessie | Ja bij phishbare MFA, niet bij phishing-resistente | Phishing-resistente MFA en Token Protection |
| Code-onderschepping | Redirect en token-endpoint | Indirect | Exacte `redirect_uri`, verplichte PKCE en `state` |
| Tokenlekken en te ruime scopes | Na tokenuitgifte | N.v.t. | Veilige opslag, minimale scopes, token binding |
| Consent phishing | Toestemmingsscherm | Ja, raakt de authenticatie niet | App-consent-policies en monitoring |

*Device code phishing* is de enige flow waarbij de aanvaller de flow start namens het slachtoffer. Het slachtoffer meldt aan op de echte Microsoft-pagina met eigen wachtwoord en MFA; de aanvaller, die het token-endpoint bevraagt, ontvangt de tokens. *Consent phishing* vraagt nooit om een wachtwoord: het slachtoffer geeft zelf toestemming aan een kwaadaardige applicatie, die daarna met `offline_access` langdurig toegang houdt.

**Uitgevoerde aanvallen — Getest.** De tokenkant van OAuth is in Pad B aangetoond: tokens verkrijgen met enkel een wachtwoord, een refresh-token hergebruiken en een PRT inwisselen voor access-tokens (hoofdstuk 5). In die tests gebruikte roadtx de wachtwoord-flow (Resource Owner Password Credentials) met de client-ID van de Microsoft Azure CLI. Dat die flow in de tenant werkte voor een account zonder MFA-eis, is zelf een bevinding: RFC 9700 raadt deze flow af.

{: .todo }
> Door de eigenaar van Pad A: is er een OAuth-specifieke aanval (device code phishing of consent phishing) live uitgevoerd? Zo ja: stappen, resultaat en screenshots.

**Phishing-resistance.** Niet inherent aanwezig. OAuth beschermt niet tegen een gebruiker die vrijwillig toestemming geeft of tegen een proxy.

**Quantum.** OAuth-tokens zijn meestal bearer-tokens. De handtekening op JWT's (RS256) en de onderliggende TLS steunen op asymmetrische cryptografie. Het protocol is algoritme-onafhankelijk; de migratie zit in de gebruikte cryptografie.

## 4.4 OpenID Connect (OIDC)

**Werking.** OIDC is een dunne authenticatielaag bovenop OAuth 2.0. Naast het access-token krijgt de applicatie een ID-token: een ondertekende JWT met claims over de gebruiker (identiteit, uitgever, tijdstip, `nonce`). Entra ID gebruikt OIDC als het moderne SSO-protocol.

**Aanvalsoppervlak.**

- *Zwakke validatie van het ID-token:* handtekening, uitgever (`iss`), doelgroep (`aud`), vervaltijd of `nonce` niet controleren; `alg: none` aanvaarden.
- *Erfenis van OAuth:* redirect-manipulatie, consent phishing en tokendiefstal gelden ook hier.
- *AiTM:* de browser-flow is via een reverse proxy te onderscheppen.

**Uitwerking.** OIDC is het protocol waarover de AiTM-aanval van Pad A loopt: de proxy onderschept de sessiecookie en tokens na een geldige aanmelding.

{: .todo }
> Door de eigenaar van Pad A: het resultaat van de AiTM-demo over OIDC (welke cookies en tokens zijn onderschept, hoe is de sessie hergebruikt), met screenshots.

**Phishing-resistance.** Zoals bij OAuth en SAML: de sterkte hangt af van de gebruikte factor en van Conditional Access met token binding.

**Quantum.** De handtekeningen op het ID-token (RSA/ECDSA) zijn post-quantum kwetsbaar. OIDC evolueert actief, waardoor een overstap naar post-quantum handtekeningen realistischer is dan bij SAML.
