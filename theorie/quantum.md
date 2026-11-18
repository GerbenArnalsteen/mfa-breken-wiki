---
title: "Quantumbestendigheid"
nav_order: 4
parent: "Theorie"
---

# Quantumbestendigheid

## 9.1 Het kader

Een cryptografisch relevante quantumcomputer bedreigt twee soorten cryptografie verschillend.

| Soort | Voorbeelden | Quantumaanval | Gevolg |
|---|---|---|---|
| Asymmetrisch | RSA, ECDSA, ECDH | Algoritme van Shor | Volledig gebroken: de privésleutel is af te leiden uit de publieke sleutel |
| Symmetrisch en hashes | AES, HMAC-SHA | Algoritme van Grover | Effectieve sleutellengte gehalveerd; op te vangen met langere sleutels |

NIST publiceerde in augustus 2024 de eerste post-quantumstandaarden: FIPS 203 (ML-KEM, sleuteluitwisseling), FIPS 204 (ML-DSA, handtekeningen) en FIPS 205 (SLH-DSA, handtekeningen). Het overgangsplan van NIST (IR 8547) voorziet dat RSA en ECC rond 2030 worden afgeraden en rond 2035 niet meer zijn toegestaan.

Voor authenticatie geldt een nuance. Bij versleuteling kan een aanvaller vandaag verkeer opslaan en later ontsleutelen. Een handtekening bij een aanmelding moet op het moment zelf vervalst worden. Het risico voor authenticatie begint dus pas op Q-day, maar dan wel voor elke sleutel die nog niet gemigreerd is, en hardware-sleutels en TPM's zijn moeilijk te vervangen.

## 9.2 Per methode en protocol

| Methode of protocol | Onderliggende cryptografie | Quantumbestendig? | Toelichting |
|---|---|---|---|
| SMS | Geen cryptografische factor | N.v.t. | Zwak om klassieke redenen |
| Software- en hardware-OTP | HMAC-SHA1 met gedeeld geheim (symmetrisch) | Ja, grotendeels | Grover halveert de sleutellengte; geen fundamentele breuk |
| Authenticator-push | Sleutelpaar voor de app-registratie | Gedeeltelijk | De zwakte is de mens en de sessie, niet de cryptografie |
| Passwordless phone sign-in | Asymmetrisch sleutelpaar op de telefoon | Nee | Half minpunt |
| WHFB | RSA of ECC in de TPM | **Nee** | Minpunt; TPM-chips zijn niet eenvoudig te vervangen |
| Passkey in Authenticator | ECDSA P-256 | **Nee** | Minpunt |
| Hardware dongle | ECDSA P-256 | **Nee** | Minpunt; de sleutel zelf moet vervangen worden |
| Tenbeo | Te bevestigen | Onbekend | Hangt af van de werking |
| CBA | RSA of ECC in X.509-certificaten | **Nee** | De hele PKI moet migreren |
| SAML | RSA/ECDSA-handtekening op de assertion | Nee | Oud protocol, trage evolutie |
| OAuth 2.0 en OIDC | RS256-handtekening op JWT's, TLS | Nee | Algoritme-onafhankelijk; migratie via de identiteitsprovider |
| PRT | Symmetrische sessiesleutel, beschermd door een asymmetrische transportsleutel | Gedeeltelijk | De bescherming van de sessiesleutel is de zwakke schakel |

## 9.3 Wat dit betekent voor de score

De methoden die vandaag het sterkst zijn, zijn precies de methoden die op asymmetrische cryptografie steunen. OTP is quantumrobuust maar vandaag al te phishen. In ons scoremodel weegt quantum daarom voor één punt: genoeg om het verschil zichtbaar te maken, niet genoeg om een vandaag onveilige methode boven een vandaag veilige te plaatsen. De FIDO Alliance werkt aan post-quantumvarianten; tot die uitgerold zijn, krijgen alle passkeys het minpunt dat de opdracht vraagt.
