---
title: "PIN tegenover wachtwoord"
nav_order: 2
parent: "Resultaten"
---

# PIN tegenover wachtwoord

De opdracht vraagt te bevestigen of te weerleggen dat een pincode van 4 karakters veiliger is dan een wachtwoord van 12 karakters in Entra ID, met eigen testresultaten.

## 7.1 Hypothese

Op entropie verliest de PIN duidelijk: 10.000 mogelijkheden tegenover vele biljoenen. De twee zijn echter geen vergelijkbare grootheden. Een wachtwoord is een netwerkgeheim dat centraal gecontroleerd wordt en vanop afstand aan te vallen is. Een WHFB-PIN ontgrendelt alleen lokaal een sleutel in de TPM van één toestel. De hypothese is dat de PIN in de praktijk veiliger is door binding, niet door entropie.

## Metingen

| Meting | Resultaat | Playbook |
|---|---|---|
| WHFB-PIN | TPM vergrendelt na 31 foute pogingen; daarna één poging per 10 minuten | [PB-08]({{ '/playbooks/pb-08-tpm-lockout/' | relative_url }}) |
| Wachtwoord | Account in de cloud vergrendeld na 10 foute pogingen | [PB-09]({{ '/playbooks/pb-09-smart-lockout/' | relative_url }}) |

## 7.4 Vergelijking

| Aspect | WHFB-PIN (4 cijfers) | Wachtwoord (12 tekens) |
|---|---|---|
| Aantal mogelijkheden | 10.000 | Zeer groot bij een willekeurig wachtwoord |
| Waar wordt geteld | In de TPM van het toestel | In Entra ID (cloud) |
| Vanop afstand aan te vallen | Nee: alleen met het toestel in handen | Ja: tegen elke Microsoft-aanmeldpagina |
| Grens bij raden | 31 pogingen, daarna één per 10 minuten | Vergrendeling na 10 pogingen |
| Tijd om alle mogelijkheden te proberen | Ongeveer 69 dagen fysieke toegang, in het slechtste geval | Niet haalbaar door raden |
| Te phishen | Nee: gaat nooit over het netwerk | Ja: via nagemaakte pagina of proxy |
| Bruikbaar na diefstal | Alleen samen met het toestel | Overal |
| Te lekken via een datalek of hergebruik | Nee | Ja |
| Gevolg van de blokkade | Alleen de PIN op dit toestel | Het account zelf, voor iedereen |

De berekening voor de PIN: na de eerste 31 pogingen blijven 9.969 mogelijkheden over, aan één per 10 minuten is dat ongeveer 69 dagen.

## 7.5 Antwoord

**De stelling is bevestigd voor de geteste configuratie, met twee voorbehouden.**

Een aanvaller breekt een wachtwoord van 12 tekens zelden door te raden. Hij verkrijgt het via phishing, een datalek of hergebruik, en kan het daarna overal gebruiken. Onze tokentests (hoofdstuk 5) tonen wat dat oplevert: met enkel een wachtwoord registreerden we een toestel en verkregen we tokens. De PIN kan niet gephisht of gelekt worden, is waardeloos zonder het toestel, en wordt door de TPM tegen raden beschermd. De veiligheid komt uit de binding aan de hardware, niet uit de lengte.

De voorbehouden:

- **Het wachtwoord bestaat nog.** WHFB voegt een sterke methode toe, maar verwijdert de zwakke niet. Zolang wachtwoord-aanmelding toegelaten is, bepaalt het wachtwoord de werkelijke sterkte van het account.
- **Lokale toegang verandert het beeld.** Wie het toestel bezit en de PIN kent of beheerdersrechten heeft, kan tokens laten uitgeven (sectie 5.4.2). De PIN beschermt tegen aanvallen van buitenaf, niet tegen een gecompromitteerd toestel.
