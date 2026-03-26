---
tags: [backend, protokoll, real-time]
datum: 2026-03-26
szint: "🧱 Brick"
kapcsolodo:
  - "[[backend/hono|Hono]]"
  - "[[backend/express|Express]]"
  - "[[frontend/nextjs|Next.js]]"
  - "[[backend/edge-function|Edge function]]"
  - "[[cloud/cloudflare|Cloudflare]]"
  - "[[database/supabase|Supabase]]"
---

# WebSocket

**Kategória:** `protokoll` (real-time kommunikáció)
**Típus:** Web standard (nem library, nem framework)

---

## Mi ez és mire jó?

A **WebSocket** egy kommunikációs protokoll, ami **állandó, kétirányú kapcsolatot** tart fenn a kliens és a szerver között. A sima HTTP-vel ellentétben a szerver is tud adatot küldeni a kliensnek (push) - nem kell a kliensnek újra meg újra kérdeznie (polling).

> [!tldr] Egy mondatban
> HTTP = levélváltás (kérdezek, válaszolsz). WebSocket = telefon (nyitva marad a vonal, bármelyik fél beszélhet bármikor).

---

## HTTP vs WebSocket vs SSE

| Szempont | HTTP (REST) | **WebSocket** | SSE (Server-Sent Events) |
|----------|-------------|---------------|--------------------------|
| **Irány** | Kliens - Szerver | Kétirányú | Szerver - Kliens |
| **Kapcsolat** | Kérés-válasz, lezárul | Állandó, nyitva marad | Állandó, egyirányú |
| **Overhead** | HTTP header minden kérésnél | Minimális (frame-ek) | Minimális |
| **Mikor használd** | CRUD, REST API | Chat, kollaboráció, gaming | Live értesítések, feed |
| **Komplexitás** | Alacsony | Közepes-magas | Alacsony |

> [!tip] Döntési fa
> **Egyirányú update kell (szerver - kliens)?** - SSE (egyszerűbb, mint WebSocket)
> **Kétirányú, real-time kell?** - WebSocket
> **Polling elég (nem kell azonnali)?** - Sima HTTP + setInterval
> **Chat, multiplayer, collaborative editing?** - WebSocket (nincs alternatíva)

---

## Library-k

| Library | Mire való | Mikor válaszd |
|---------|-----------|---------------|
| **Socket.io** | Full-featured WS library (auto reconnect, rooms, fallback) | Legtöbb esetben - production-ready |
| **ws** | Raw WebSocket Node.js-hez | Ha nem kell Socket.io overhead |
| **[[cloud/cloudflare|Cloudflare]] Durable Objects** | Edge-natív WebSocket kezelés | Cloudflare Workers-ön |
| **Partykit / PartyServer** | Collaborative real-time (hosted) | Gyors prototípus, multiplayer |

**Socket.io vs raw WebSocket:**
- Socket.io = automatikus reconnect, room-ok, namespace-ek, fallback polling-ra
- Raw `ws` = könnyebb, gyorsabb, de mindent magad implementálsz

---

## Használati esetek

| Use case | Példa | Ajánlott megoldás |
|----------|-------|--------------------|
| **Chat** | Slack/Discord klón | Socket.io (room-ok, presence) |
| **Live értesítések** | "Új rendelés érkezett!" | SSE is elég, de WebSocket is jó |
| **Collaborative editing** | Google Docs klón | Partykit / CRDT + WebSocket |
| **Live dashboard** | Real-time analytics | SSE vagy WebSocket |
| **Gaming** | Multiplayer | Raw WebSocket (latency-kritikus) |
| **Live cursor / presence** | "Ki szerkeszt most?" | Socket.io / Partykit |

---

## Mikor NE használj WebSocket-et

- **Sima CRUD app** - HTTP tökéletesen elég
- **Egyirányú értesítés** - SSE egyszerűbb és elég
- **[[backend/edge-function|Edge function]]-ök** - a legtöbb edge function nem tart állandó kapcsolatot (kivétel: Cloudflare Durable Objects)
- **Mobile app, instabil hálózat** - polling megbízhatóbb lehet (vagy Socket.io auto-reconnect)

---

## AI-natív fejlesztés

A WebSocket implementáció komplex boilerplate-et igényel (connection handling, reconnect, room management), ami jó AI-generálási feladat. A kulcs: pontosítsd, hogy Socket.io-t vagy raw WebSocket-et akarsz, mert a kettő teljesen más kódot eredményez.

> [!tip] Hogyan használd AI-val
> - *"Socket.io szerver Hono-val: chat room-ok, auto reconnect, és egy React hook ami a room-ba csatlakozik"*
> - *"Socket.io vs WebSocket - pontosítsd! Ha 'WebSocket'-et írsz, az AI raw WebSocket implementációt ad. Ha Socket.io-t akarsz (auto reconnect, room-ok), azt explicit írd"*
> - *"Cloudflare Durable Objects WebSocket handler: Hibernation API-val, ami csak üzenet érkezésekor ébred fel"*
