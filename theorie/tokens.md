---
title: "Tokens"
nav_order: 2
parent: "Theorie"
---

# Tokens

| Token | Waarvoor | Levensduur in Entra ID (standaard) | Waarde voor een aanvaller |
|---|---|---|---|
| ID-token | Bewijst identiteit aan de applicatie | Ongeveer 1 uur | Laag |
| Access-token | Toegang tot één API of resource | 60 tot 90 minuten | Middel: kortstondig |
| Refresh-token | Nieuwe access-tokens zonder herauthenticatie | Tot 90 dagen | Hoog: langdurige toegang |
| Primary Refresh Token (PRT) | SSO over alle Entra-applicaties op een toestel | 14 dagen, doorlopend vernieuwd | Zeer hoog |

Een gewoon refresh-token hoort bij één applicatie. Een PRT hoort bij een toestel en geeft SSO naar vrijwel elke Entra-applicatie. Een ruwe PRT is echter niet rechtstreeks bruikbaar: de uitwisseling naar een access-token vereist een aanvraag die getekend is met de sessiesleutel van het toestel. Dat verklaart het kernonderscheid van dit hoofdstuk: **"PRT stelen" (de sleutels exporteren) is iets anders dan "PRT gebruiken" (een token laten uitgeven via een geautoriseerd kanaal).**

## Wat onze tests hierover tonen

Een token is een toegangsbewijs dat losstaat van de manier waarop de gebruiker zich aanmeldde. Zonder binding aan een toestel is elk refresh-token overdraagbaar. Met TPM-binding is de PRT niet te stelen, maar wel te gebruiken door wie code kan uitvoeren in de sessie van de gebruiker. En wie een eigen toestel kan registreren, krijgt een PRT die volledig van hem is. Hoofdstuk 8 toont welke verdedigingen elk van deze drie gevallen tegenhouden.

De aanvallen zelf staan in de [playbooks]({{ '/playbooks/' | relative_url }}).
