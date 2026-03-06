---
tags:
  - devops
  - infrastruktura
datum: 2026-02-12
szint: "🏗️ Builder"
kapcsolodo:
  - "[[cloud/docker-compose|Docker Compose]]"
  - "[[cloud/kubernetes-bevezeto|Kubernetes bevezeto]]"
  - "[[cloud/devops|DevOps]]"
---

# Cluster

## Összefoglaló

Több onallo gep (vagy process) osszefogasa, amelyek kivulrol egyetlen rendszerkent viselkednek.
Olyankor kell, amikor az egyik leall, akkor a másik valtsa ki.
Downtime minimalizalas eseten fontos.

Az kozos mindben: **redundancia** (ha egy kiesik, megy tovabb), **skálázhatosag** (több node = több kapacitas), és **elosztott működes** (a munkat megosztjak)

## Jegyzetek

**1. Infrastruktura / DevOps cluster**

Az alap koncepcio: van több fizikai vagy virtuális szervered, és egy orchestrator (vezenylö) szoftver kezeli, hogy melyik gépen mi fusson. Ha egy gep kiesik, automatikusan athelyezi a munkat egy másikra.

Valós példa: egy webshop Black Friday-re készül. Normal napokon 1 szerver birja, de novemberben 10x annyi a forgalom. Kubernetes cluster-ben beállítjak, hogy ha a CPU terheles eléri a 70%-ot, indítson új pod-okat (konténer példanyokat) automatikusan, akar új node-okon. Black Friday utan visszaskalaz. A Kubernetes mögött mondjuk 5 Hetzner szerver van, amik egyutt a cluster node-jai.

Másik példa: a Netflix. Több ezer node-on futtatjak a szolgáltatasaikat AWS-en. Ha egy availability zone kiesik (mondjuk egy egész adatkozpont), a többi zone atveszi a forgalmat. A felhasználó ebbol semmit nem vesz eszre.

---

**2. Adatbázis cluster**

Itt az adat a kritikus. Egy DB szerver = single point of failure. Ha meghal a diszk, elveszhet az adat. Egy DB cluster-ben több instance tartja szinkronban ugyanazt az adatot, és/vagy megosztjak a terhelest.

Valós példa: egy bank PostgreSQL cluster-t használ. Van egy primary node (ide mennek az irasok: új utalas, egyenleg módosítas) és 2 replica node (ezekrol olvasnak: egyenlegek lekérdezése, kimutatasok). Ha a primary meghal, az egyik replica automatikusan atveszi a szerepet -- ezt hivjak failover-nek. Az ugyfel nem veszi eszre, hogy bármi tortent.

Másik példa: a Redis Cluster. Egy nagy e-commerce site a session adatokat és a kosar tartalmat Redis-ben tarolja. Egyetlen Redis instance-ban max ~25GB fer el, de nekik 100GB kell. Redis Cluster-ben az adatot 6 node-ra shardoljak (szetdaraboljak) -- mindegyik node a teljes adatnak egy szeletet tarolja, és mindegyiknek van egy replikaja is. Ha egy node kiesik, a replikaja atveszi.

---

**3. HPC (High Performance Computing) cluster**

Itt nem a rendelkezesre allas a lényeg, hanem a nyers szamitasi kapacitas. Egy feladatot szetdarabolnak, és sok gep párhuzamosan dolgozik rajta.

Valós példa: az idojaras-elorejelzes. Szuperszamitogep cluster-eken futtatjak az elorejelzo modelleket. A legkor állapotat egy 3D racsra bontjak (mondjuk 1km-és felbontassal), és minden racspont szamitasat kulon CPU core vegzi. Több ezer core dolgozik párhuzamosan, hogy a holnapi idojaras 1 ora alatt kiszamithato legyen, nem 1 het alatt.

Másik példa: GPT modellek tanitasa. Több ezer GPU-t kotnek ossze egy cluster-be. Egyetlen GPU-n a training evekig tartana. Ezer GPU-n hetekig tart. Az adat és a modell sulyai szet vannak osztva a GPU-k kozott, mindegyik a saját reszet szamolja, aztán szinkronizalnak.

---

**4. Hálózati / elosztott adatkezelo cluster**

Itt az a lényeg, hogy hatalmas mennyisegu adatot kell tárolnii, keresni vagy feldolgozni, és egyetlen gep nem birna sem tárolnii, sem feldolgozni. Az adatot szetszorjak (shard-oljak) a node-ok kozott, és a kereses/feldolgozas párhuzamosan fut rajtuk.

Valós példa: az Elasticsearch cluster egy nagy news portálnal. Több millio cikk van, és a keresésnek 200ms alatt kell válaszolnia. Az adatot 5 shard-ra bontjak, mindegyik shard egy kulon node-on el. Amikor beirod a keresest, mind az 5 node egyszerre keres a saját shard-jaban, az eredmenyeket összegyűjti egy coordinator node, és visszaadja neked. Egy gépen ez 2 masodperc lenne, 5 gépen 400ms.

Másik példa: Hadoop/Spark cluster. Egy telekommunikacios ceg naponta több TB hivasadatot general. Ezt egy Hadoop cluster-be toltik, ahol az adatot 20+ node-on taroljak elosztva (HDFS). Amikor elemzest futtatnak, a Spark job minden node-on lokálisan dolgozza fel a saját adatszeletat, és csak az eredmenyt küldi vissza. Nem kell az egész adatot egy helyre mozgatni.

## Fo tanulsagok

**Mikor melyiket:**
**Infra/DevOps cluster** -- amikor az app nem allhat le, vagy egy szerver nem birja a forgalmat. Ha "0 downtime kell" vagy "lassan tolt be mert sokan használjak", ez kell. SME-knel ritkan, de ha SLA-t igerunk (pl. 99.9% uptime), akkor igen.

**DB cluster** -- amikor az adatvesztes elfogadhatatlan, vagy az olvasasi terheles nagy. Ha kritikus az adatbázis (pl. rendeleskezelo, penzugyi rendszer), legalabb egy primary + replica felallas kell. A legegyszerubb ut: managed DB szolgáltatas, ne magad cluster-ezz.

**HPC cluster** -- valószinuleg nem relevans. Ez kutatas, ML training, szimulációk.

**Elosztott adat cluster** -- amikor az adatmennyiseg kinovi az egy gepet, vagy a keresesnek gyorsnak kell lennie nagy adaton. Ha par ezer rekord van, PostgreSQL fulltext search eleg. Ha millios nagyságrendu adaton kell keresni, akkor jon az Elasticsearch. A legtobb projektnél nem kell.

**Gyakorlati szabaly:** az esetek 90%-aban egyetlen szerver + rendszeres backup + gyors ujratelepitesi terv elegendo. A cluster overhead-et (költség, komplexitas, karbantartas) csak akkor vallald be, ha van ra konkret uzleti indok.

## Kapcsolodo anyagok

- [[cloud/docker-compose|Docker Compose]]
- Kubernetes gyakorlat OrbStack -- cluster koncepcio a gyakorlatban, OrbStack egyetlen node-os klaszterrel
