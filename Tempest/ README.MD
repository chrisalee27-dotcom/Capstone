# Tempest Investigation

This investigation reconstructs the attacker activity in the Tempest SOC Level 1 challenge.

## Initial Access

Malicious document:

free_magicules.doc

## Exploit

CVE-2022-30190 (Follina)

Command executed:

msdt.exe

## Command and Control

Attacker IP:

167.71.199.191

Domains:

phishteam.xyz
resolvecyber.xyz

## Privilege Escalation

Privilege abused:

SeImpersonatePrivilege

Tool used:

PrintSpoofer

## Pivoting

Reverse SOCKS proxy using:

Chisel

## Persistence

Windows service created:

TempestUpdate2
