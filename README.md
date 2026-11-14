# DHCP Emulator - GUI Server + GUI Client (Network / Transport / Application layer project)

Python 3.8+, standard library only (Tkinter). On Linux you may need:  sudo apt install python3-tk

## Run (two separate programs)
    python run_server_gui.py     # 1) start this first, press "Start server"
    python run_client_gui.py     # 2) press "Obtain IP (DORA)"   (open several windows = several hosts)

Use 127.0.0.1 to test on one PC. For two PCs in the lab: set the server's *Listen address* to 0.0.0.0
(Configuration tab) and type the server PC's IP in the client's "DHCP server" box.

## Server GUI
* Packet Capture tab: Wireshark-like view
  - packet list (No., Time, Source, Destination, Protocol, Length, Info), colour-coded by message type
  - protocol tree (Frame / IPv4 / UDP / DHCP with every field and option)
  - hex dump; click any field to highlight its bytes
  - filter box (all words must match: type, MAC, IP, transaction id), auto-scroll, clear, export CSV
* Leases tab: live lease table (leased / offered / static), revoke button, server event log
* Configuration tab: listen address/port, pool, mask, router, DNS, lease time, reservations

## Client GUI
* Settings: server host:port, host name, MAC (random or custom), auto-renew toggle
* State machine strip: INIT -> SELECTING -> REQUESTING -> BOUND -> RENEWING
* Actions: Obtain (DORA), Renew, Release, Decline; current lease panel with countdown
* Its own Wireshark-style capture of the exchange + event log

## Project layout
    dhcp/            protocol + logic (no GUI imports)
      constants.py   packet.py   config.py   pool.py   server.py   client.py
      capture.py     packet recorder (what the Capture tab shows)
      dissect.py     Wireshark-style dissector + hex dump
      eventlog.py    utils.py
    gui/             server_app.py  client_app.py  capture_view.py  widgets.py
    tests/           packet, pool, capture/dissector, integration (real UDP sockets)
    run_server_gui.py   run_client_gui.py

Tests (optional):  python -m unittest discover -s tests -t .

## Emulation notes
Real DHCP = UDP 67/68 + LAN broadcast (needs admin rights and a real NIC). Here the messages travel over
ordinary UDP sockets (default 127.0.0.1:6767). The DHCP payload is byte-accurate RFC 2131; the capture
adds the IPv4/UDP view (0.0.0.0 -> 255.255.255.255, ports 68 -> 67) and marks it "[emulated]".

## ARP (later)
Add an `arp/` package next to `dhcp/` and a new `gui/arp_app.py`; capture_view/dissect can be reused.
