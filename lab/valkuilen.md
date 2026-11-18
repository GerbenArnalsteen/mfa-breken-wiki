---
title: "Valkuilen"
nav_order: 5
parent: "Lab"
---

# Valkuilen

Problemen die we tegenkwamen, met oorzaak en oplossing.

| # | Valkuil | Oorzaak | Oplossing |
|---|---|---|---|
| 1 | Twijfel over het MDM-gebruikersbereik | Stond al op "All" in de gedeelde tenant | Niet aanpassen; policies op de eigen groep scopen |
| 2 | Temporary Access Pass werkte niet | Policy niet actief | Authentication methods policy voor TAP inschakelen |
| 3 | roadtx faalde bij de eerste aanmelding | Tijdelijk wachtwoord bij een nieuw account | Wachtwoord eerst wijzigen via myaccount.microsoft.com |
| 4 | Geen lokale adminrechten na Entra-join | Standaardgedrag | Rol "Microsoft Entra Joined Device Local Administrator"; volledig af- en aanmelden |
| 5 | OOBE dwong een Microsoft-account af | Netwerkverbinding tijdens OOBE | Netwerkadapter loskoppelen vóór de eerste start |
| 6 | Geen PIN-prompt na Entra-join | Geen actieve WHFB-policy | Policy aanmaken in Intune en laten synchroniseren |
| 7 | `pip` niet herkend | Python Install Manager zet `Scripts` niet op PATH | `py -3.12 -m pip` of PATH handmatig aanvullen |
| 8 | Bouwfout bij roadtx (`zstandard`) | Geen kant-en-klaar pakket voor nieuwe Python-versies | Python 3.12 gebruiken |
| 9 | `py -m roadtx` werkt niet | Geen `__main__.py` | `roadtx` rechtstreeks aanroepen |
| 10 | TokenTacticsV2 "not digitally signed" | Mark of the Web | `Get-ChildItem -Recurse \| Unblock-File` |
| 11 | TokenTacticsV2: pad niet gevonden | Dubbele mapnesteling in de ZIP | Het binnenste pad gebruiken |
| 12 | AADInternals niet zichtbaar | Gescheiden modulemappen voor PowerShell 5.1 en 7 | Altijd `pwsh` gebruiken, `-Scope AllUsers` |
| 13 | `Get-AADIntUserPRTKeys -CloudAP` bestaat niet | Hernoemd vanaf v0.9.7 | `Get-AADIntUserPRTKeysFromCloudAP` |
| 14 | `Set-MpPreference` zonder effect | Tamper Protection | Exclusies alleen via Intune |
| 15 | Helper-programma verwijderd ondanks exclusie | Willekeurige naam; temp-mappen niet uitgesloten | Vier paden uitsluiten; module daarna opnieuw installeren |
| 16 | `roadtx -u ...` geeft "invalid choice" | Subcommando ontbreekt | `roadtx gettokens -u ...` |
| 17 | MS Graph-token via BrowserCore faalt | Redirect-incompatibiliteit AADInternals v0.9.8 met PowerShell 7 | Niet opgelost; Exchange Online en Intune MDM werken |
| 18 | `Invoke-RefreshToMSGraphToken` geeft AADSTS70000 | Tokenformaat van roadtx | `roadtx refreshtokento` gebruiken |
| 19 | Token Protection blokkeert niets | Aanvraag niet als Windows herkend | Bij tests `-ua` met een Windows-user-agent; als verdediging onbekende platformen blokkeren |
| 20 | Maar één regel in het aanmeldlogboek | Non-interactive aanmeldingen worden gegroepeerd en lopen achter | Regel openklappen; tot 15 minuten wachten |
| 21 | Registratie lukt ondanks MFA-policy | Oud token in `.roadtools_auth` | Bestand verwijderen vóór een hertest |
| 22 | Policy lijkt niet te werken direct na aanmaken | Policy nog niet actief | Enkele minuten wachten; tijdstip van aanmaken noteren |
