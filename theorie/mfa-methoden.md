---
title: "MFA-methoden"
nav_order: 3
parent: "Theorie"
---

# MFA-methoden

## 6.1 Twee eigenschappen die de sterkte bepalen

MFA combineert kennis (wachtwoord, PIN), bezit (telefoon, sleutel, certificaat) en inherentie (biometrie). De sterkte van een methode hangt vooral af van twee eigenschappen:

- **Phishing-resistance.** Kan een nagemaakte site of een reverse proxy de factor doorsturen? Methoden met een deelbaar geheim (een code intikken, een melding goedkeuren) zijn phishbaar. Methoden die cryptografisch aan het echte domein gebonden zijn (FIDO2, passkeys) zijn dat niet.
- **Token binding.** Na elke aanmelding blijft een token of sessiecookie achter. Zonder binding aan het toestel is dat token te stelen en elders te gebruiken, welke MFA-methode er ook aan voorafging.

| Groep | Methoden | Phishbaar via AiTM? |
|---|---|---|
| Deelbaar geheim | SMS, software-OTP, hardware-OTP | Ja |
| Goedkeuring | Authenticator-push, passwordless phone sign-in | Ja |
| Domeingebonden sleutel | WHFB, passkey in Authenticator, hardware dongle | Nee |
| Biometrisch (werking te bevestigen) | Tenbeo | Onbekend |

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

## 6.3 MFA met SMS

**Werking.** Na het wachtwoord stuurt Entra ID een eenmalige code per sms naar het geregistreerde nummer. De bezitsfactor is de simkaart.

**Aanvalsoppervlak.** De code is een deelbaar geheim: via een proxy tikt de gebruiker ze gewoon door. Daarnaast kan een aanvaller het nummer laten overzetten naar een eigen simkaart (SIM-swapping), laat het telecomnetwerk in bepaalde gevallen onderschepping toe (SS7), en kan malware op de telefoon sms'en lezen.

**Aanval en demo (Pad A) — Aan te vullen.** De workaround is AiTM met Evilginx3: de victim meldt aan via de proxy-URL, tikt de sms-code in, en de proxy onderschept de sessiecookie die Entra ID na de geldige MFA-stap uitgeeft. De aanvaller laadt die cookie in een eigen browser.

{: .todo }
> Door de eigenaar van Pad A: startcondities, stappen, resultaat (gelukt of mislukt) en screenshot of video van de SMS-demo.

**Verdediging.** SMS uitschakelen als MFA-methode; phishing-resistente MFA afdwingen met Authentication Strengths. Token Protection beperkt de schade na diefstal, maar dekt browsersessies niet (sectie 8.6).

**Quantum.** Niet relevant voor de factor zelf: de zwakte is de telecomlaag en het deelbare geheim, niet de cryptografie.

**Score: 2/10.** Phishing-resistance 0: de code is volledig door te sturen en SIM-swapping maakt zelfs een proxy overbodig. Binding 0: het nummer is overdraagbaar en hangt af van de processen van een telecomoperator. Tokenrisico 1: de sessie na aanmelding is niet gebonden. Quantum 1: geen afhankelijkheid van asymmetrische cryptografie. SMS is de zwakste methode in de opdracht en wordt door NIST en Microsoft afgeraden.

## 6.4 MFA met software-OTP

**Werking.** Een app (Microsoft Authenticator, Google Authenticator) bewaart een gedeeld geheim (seed) en berekent daaruit met de huidige tijd elke 30 seconden een code van 6 cijfers (TOTP, RFC 6238).

**Aanvalsoppervlak.** De code blijft een deelbaar geheim en is via AiTM binnen het geldigheidsvenster te gebruiken. Wie de seed bemachtigt (uit een back-up, een foto van de QR-code of een gecompromitteerde telefoon) genereert zelf codes.

**Aanval en demo (Pad A) — Aan te vullen.** Dezelfde AiTM-keten als bij SMS; alleen het testaccount wisselt.

{: .todo }
> Door de eigenaar van Pad A: resultaat en bewijs van de software-OTP-demo.

**Verdediging.** Zoals bij SMS. De seed beschermen met toestelversleuteling en app-vergrendeling.

**Quantum.** TOTP steunt op een symmetrisch geheim en HMAC. Het algoritme van Grover halveert alleen de effectieve sleutellengte; de cryptografie is hier niet de zwakke schakel.

**Score: 3/10.** Phishing-resistance 0,5: even phishbaar via AiTM als SMS, maar zonder SIM-swap en SS7. Binding 0,5: de seed zit op één toestel, maar is kopieerbaar. Tokenrisico 1. Quantum 1.

## 6.5 MFA met hardware-OTP

**Werking.** Een los OATH-token toont de TOTP-code op een eigen scherm. De seed zit in het apparaat, dat offline is.

**Aanvalsoppervlak.** Tegen AiTM biedt de hardware geen extra bescherming: het blijft een code die de gebruiker intikt. Het voordeel zit elders: de seed is niet via malware of een back-up te bemachtigen.

**Aanval en demo (Pad A) — Aan te vullen.**

{: .todo }
> Door de eigenaar van Pad A: resultaat en bewijs van de hardware-OTP-demo.

**Verdediging.** Zoals bij SMS.

**Quantum.** Zoals software-OTP: symmetrisch, relatief quantumrobuust.

**Score: 3,5/10.** Phishing-resistance 0,5. Binding 1: de seed is hardwarematig opgeslagen en niet kopieerbaar. Tokenrisico 1. Quantum 1. Het halve punt verschil met software-OTP weerspiegelt alleen de betere opslag van de seed; tegen de belangrijkste aanval zijn beide even zwak.

## 6.6 MFA met Microsoft Authenticator (push)

**Werking.** Na het wachtwoord krijgt de gebruiker een melding in de Authenticator-app. Met number matching moet de gebruiker een getal van het aanmeldscherm overtikken in de app.

**Aanvalsoppervlak.** Number matching beperkt MFA fatigue (blijven pushen tot de gebruiker goedkeurt) sterk. Het stopt AiTM niet: de proxy toont de gebruiker het juiste getal, want de gebruiker keurt een echte aanmelding goed die via de proxy loopt.

**Aanval en demo (Pad A) — Aan te vullen.**

{: .todo }
> Door de eigenaar van Pad A: resultaat en bewijs van de push-demo, met vermelding of number matching aanstond.

**Verdediging.** Zoals bij SMS; number matching aan laten staan tegen fatigue.

**Quantum.** De registratie in de app steunt op een sleutelpaar, maar de zwakte is de mens en de sessie. Quantum verandert het risicoprofiel nauwelijks.

**Score: 4/10.** Phishing-resistance 1: beter dan OTP tegen fatigue en raden, maar niet bestand tegen een proxy. Binding 1: de goedkeuring hangt aan één geregistreerde app op één toestel. Tokenrisico 1. Quantum 1.

## 6.7 Passwordless MFA

**Werking.** "Passwordless" is een categorie: het wachtwoord verdwijnt als primaire factor. In Entra ID gaat het om WHFB, FIDO2-sleutels, passkeys in Authenticator, CBA en passwordless phone sign-in. De eerste vier komen elders in dit rapport aan bod; deze sectie beoordeelt phone sign-in, waarbij de gebruiker alleen zijn gebruikersnaam ingeeft en de aanmelding in de app bevestigt met number matching en biometrie of PIN.

**Aanvalsoppervlak.** Er is geen wachtwoord meer dat gelekt, geraden of gephisht kan worden. De bevestiging in de app is echter niet aan het domein gebonden, dus AiTM werkt zoals bij push. Verder is de methode zo sterk als de zwakste toegelaten terugvaloptie: kan de gebruiker terugvallen op SMS of een wachtwoord, dan zakt de effectieve sterkte naar die methode. Onze eigen meting bevestigt dat principe voor WHFB: het wachtwoord bleef werken terwijl de PIN vergrendeld was (hoofdstuk 7).

**Aanval en demo — Aan te vullen.**

{: .todo }
> Is passwordless phone sign-in via de proxy getest, en met welk resultaat?

**Verdediging.** Terugvalmethoden beperken; phishing-resistente Authentication Strength afdwingen.

**Quantum.** De app-registratie gebruikt een asymmetrisch sleutelpaar; dat geeft een half minpunt.

**Score: 4,5/10.** Phishing-resistance 1,5: geen wachtwoord meer om te stelen, maar de bevestiging is via een proxy door te sturen. Binding 1,5: sleutel op het toestel, ontgrendeld met biometrie. Tokenrisico 1. Quantum 0,5.

## 6.8 Passkey met Windows Hello for Business (WHFB)

**Werking.** Bij inschrijving maakt WHFB een asymmetrisch sleutelpaar aan. De privésleutel zit in de TPM van het toestel en verlaat die nooit. De gebruiker ontgrendelt de sleutel lokaal met een PIN of biometrie. De PIN gaat niet over het netwerk. De aanmelding is een challenge-response met die sleutel, en levert op een Entra-joined toestel een PRT op.

**Aanvalsoppervlak.** De sleutel is niet exporteerbaar en niet op afstand te phishen. Het realistische pad is lokale compromittering: malware of een aanvaller met beheerdersrechten op het toestel.

**Aanval en demo (Pad B) — Getest.** Vier tests, alle op de victim-VM met vTPM:

| Test | Resultaat | Verwijzing |
|---|---|---|
| Sleutels en PRT exporteren (AADInternals) | Geblokkeerd door TPM-binding, Defender, Tamper Protection en Intune | 5.4.1 |
| PRT gebruiken via BrowserCore | Geslaagd: token voor Exchange Online en Intune MDM | 5.4.2 |
| Dezelfde aanval met Token Protection | Geblokkeerd (foutcode 530084) | 8.2 |
| PIN raden | TPM vergrendelt na 31 pogingen, daarna één per 10 minuten | 7 |

**Verdediging.** Token Protection stopt de BrowserCore-aanval voor de beschermde resources. Wachtwoord-aanmelding uitschakelen voorkomt dat het wachtwoord als zwakkere zijdeur blijft bestaan. Lokale beheerdersrechten beperken neemt de uitgangspositie van de aanval weg.

**Quantum.** WHFB steunt op asymmetrische cryptografie (RSA of ECC). Vandaag veilig, maar kwetsbaar voor Shor. Dit is het minpunt dat de opdracht noemt.

**Score: 8/10.** Phishing-resistance 4: geen deelbaar geheim, de sleutel is aan het toestel gebonden. Binding 3: onze tests toonden dat geen enkele methode de sleutels uit de TPM kreeg. Tokenrisico 1: zonder Token Protection kon code in de gebruikerssessie een bruikbaar token laten uitgeven. Quantum 0. Met Token Protection en zonder wachtwoord-terugval zou het tokenrisico naar 2 stijgen.

## 6.9 Passkey met Microsoft Authenticator

**Werking.** De Authenticator-app bewaart een toestelgebonden FIDO2-passkey in de beveiligde opslag van de telefoon. Aanmelden op een pc gebeurt via een challenge die de gebruiker op de telefoon bevestigt met biometrie of PIN.

**Domain binding.** FIDO2 bindt de passkey aan de origin. De browser geeft het echte domein door aan de authenticator, die alleen tekent voor het domein waarvoor de passkey is aangemaakt. Een phishingpagina op een ander domein krijgt geen geldige assertion.

**Proximity check.** Bij cross-device aanmelding scant de gebruiker een QR-code. Het hybride transport gebruikt daarna Bluetooth Low Energy om te bevestigen dat pc en telefoon fysiek dicht bij elkaar zijn. Dat moet relaying over het internet tegenhouden. Een waarschuwing uit de literatuur: in 2025 claimde Expel een geslaagde relay in de PoisonSeed-campagne en trok die claim later in, omdat de proximity-check wel degelijk had standgehouden. Een proxy kan een QR-code doorsturen zonder dat de Bluetooth-controle omzeild is.

**Aanval en demo (Pad C) — Aan te vullen.**

{: .todo }
> Door de eigenaar van Pad C: (1) AiTM-poging tegen de passkey en het exacte punt waar de flow afbreekt; (2) de proximity-test met de toestellen bewust ver uit elkaar of afgeschermd, en of de aanmelding dan nog slaagt; (3) wat niet lukte en wat theoretisch mogelijk zou moeten zijn.

**Verdediging.** Attestation afdwingen zodat alleen goedgekeurde authenticators toegelaten worden; terugvalmethoden beperken.

**Quantum.** Asymmetrisch (ECDSA P-256): vandaag veilig, minpunt richting Q-day.

**Score: 7,5/10 (voorlopig).** Phishing-resistance 4. Binding 2,5: toestelgebonden, maar op een telefoon die een groter aanvalsoppervlak heeft dan een speciale chip, en met een draadloos kanaal bij cross-device gebruik. Tokenrisico 1. Quantum 0.

## 6.10 Passkey met hardware dongle

**Werking.** Een FIDO2-sleutel (YubiKey) bewaart de privésleutel in een beveiligde chip. Aanmelden vereist de sleutel inpluggen of aantikken, plus een aanraking en eventueel een PIN.

**Domain binding en relaying.** De sleutel weigert te tekenen voor een verkeerde origin. Voor relaying zou een aanvaller het USB- of NFC-verkeer tussen de sleutel en een pc op afstand moeten doorsturen. Zelfs als dat lukt, tekent de sleutel alleen voor het domein dat de browser doorgeeft, en is een fysieke aanraking nodig.

**Aanval en demo (Pad C) — Aan te vullen.**

{: .todo }
> Door de eigenaar van Pad C: (1) AiTM-poging tegen de dongle en het faalpunt; (2) het relay-onderzoek: is een poging gedaan, waar liep ze vast, wat zou theoretisch nog kunnen; (3) screenshots of video.

**Verdediging.** PIN op de sleutel verplichten tegen verlies of diefstal; attestation afdwingen.

**Quantum.** Asymmetrisch: de huidige generatie sleutels verdient het minpunt.

**Score: 8/10 (voorlopig).** Phishing-resistance 4: de referentie voor phishing-resistente MFA. Binding 3: sleutel in een speciale chip, niet exporteerbaar, fysieke aanwezigheid vereist. Tokenrisico 1: de browsersessie na aanmelding is niet gebonden. Quantum 0.

## 6.11 Passkey met Tenbeo

**Werking volgens de leverancier.** Tenbeo gebruikt de hartslag- of ECG-signatuur als biometrische factor, lokaal geverifieerd op een wearable, en positioneert zich op continue authenticatie: doorlopend nagaan dat de aangemelde persoon nog de gebruiker is.

**Kernvraag.** Levert Tenbeo een echte FIDO2-passkey met domain binding, of legt het biometrie bovenop een andere factor? Dat bepaalt de phishing-resistance volledig. Bij een biometrische factor zijn daarnaast de liveness-detectie en het gedrag bij signaalonderbreking de punten om te testen. Continue verificatie is interessant tegen tokendiefstal: een gekaapte sessie zou afgesloten moeten worden wanneer het signaal van de echte gebruiker ontbreekt.

**Aanval en demo — Aan te vullen.**

{: .todo }
> Het antwoord van Tenbeo op de vraag naar de werking, de testopstelling, de uitgevoerde tests (AiTM, token na aanmelding, signaalonderbreking) en de resultaten. Deze claims komen van de leverancier en zijn niet onafhankelijk geverifieerd.

**Quantum.** Hangt af van de onderliggende cryptografie; vast te stellen zodra de werking bevestigd is.

**Score: nog niet toegekend.** Zonder bevestiging van de werking is geen onderbouwde score mogelijk.
