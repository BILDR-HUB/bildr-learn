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

## Osszefoglalo

Tobb onallo gep (vagy process) osszefogasa, amelyek kivulrol egyetlen rendszerkent viselkednek.
Olyankor kell, amikor az egyik leall, akkor a masik valtsa ki.
Downtime minimalizalas eseten fontos.

Az kozos mindben: **redundancia** (ha egy kiesik, megy tovabb), **skalazhatosag** (tobb node = tobb kapacitas), es **elosztott mukodes** (a munkat megosztjak)

## Jegyzetek

**1. Infrastruktura / DevOps cluster**

Az alap koncepcio: van tobb fizikai vagy virtualis szervered, es egy orchestrator (vezenylö) szoftver kezeli, hogy melyik gepen mi fusson. Ha egy gep kiesik, automatikusan athelyezi a munkat egy masikra.

Valos pelda: egy webshop Black Friday-re keszul. Normal napokon 1 szerver birja, de novemberben 10x annyi a forgalom. Kubernetes cluster-ben beallitjak, hogy ha a CPU terheles eleri a 70%-ot, inditson uj pod-okat (kontener peldanyokat) automatikusan, akar uj node-okon. Black Friday utan visszaskalaz. A Kubernetes mogott mondjuk 5 Hetzner szerver van, amik egyutt a cluster node-jai.

Masik pelda: a Netflix. Tobb ezer node-on futtatjak a szolgaltatasaikat AWS-en. Ha egy availability zone kiesik (mondjuk egy egesz adatkozpont), a tobbi zone atveszi a forgalmat. A felhasznalo ebbol semmit nem vesz eszre.

---

**2. Adatbazis cluster**

Itt az adat a kritikus. Egy DB szerver = single point of failure. Ha meghal a diszk, elveszhet az adat. Egy DB cluster-ben tobb instance tartja szinkronban ugyanazt az adatot, es/vagy megosztjak a terhelest.

Valos pelda: egy bank PostgreSQL cluster-t hasznal. Van egy primary node (ide mennek az irasok: uj utalas, egyenleg modositas) es 2 replica node (ezekrol olvasnak: egyenlegek lekerdezese, kimutatasok). Ha a primary meghal, az egyik replica automatikusan atveszi a szerepet -- ezt hivjak failover-nek. Az ugyfel nem veszi eszre, hogy barmi tortent.

Masik pelda: a Redis Cluster. Egy nagy e-commerce site a session adatokat es a kosar tartalmat Redis-ben tarolja. Egyetlen Redis instance-ban max ~25GB fer el, de nekik 100GB kell. Redis Cluster-ben az adatot 6 node-ra shardoljak (szetdaraboljak) -- mindegyik node a teljes adatnak egy szeletet tarolja, es mindegyiknek van egy replikaja is. Ha egy node kiesik, a replikaja atveszi.

---

**3. HPC (High Performance Computing) cluster**

Itt nem a rendelkezesre allas a lenyeg, hanem a nyers szamitasi kapacitas. Egy feladatot szetdarabolnak, es sok gep parhuzamosan dolgozik rajta.

Valos pelda: az idojaras-elorejelzes. Szuperszamitogep cluster-eken futtatjak az elorejelzo modelleket. A legkor allapotat egy 3D racsra bontjak (mondjuk 1km-es felbontassal), es minden racspont szamitasat kulon CPU core vegzi. Tobb ezer core dolgozik parhuzamosan, hogy a holnapi idojaras 1 ora alatt kiszamithato legyen, nem 1 het alatt.

Masik pelda: GPT modellek tanitasa. Tobb ezer GPU-t kotnek ossze egy cluster-be. Egyetlen GPU-n a training evekig tartana. Ezer GPU-n hetekig tart. Az adat es a modell sulyai szet vannak osztva a GPU-k kozott, mindegyik a sajat reszet szamolja, aztán szinkronizalnak.

---

**4. Halozati / elosztott adatkezelo cluster**

Itt az a lenyeg, hogy hatalmas mennyisegu adatot kell tarolni, keresni vagy feldolgozni, es egyetlen gep nem birna sem tarolni, sem feldolgozni. Az adatot szetszorjak (shard-oljak) a node-ok kozott, es a kereses/feldolgozas parhuzamosan fut rajtuk.

Valos pelda: az Elasticsearch cluster egy nagy news portálnal. Tobb millio cikk van, es a keresésnek 200ms alatt kell valaszolnia. Az adatot 5 shard-ra bontjak, mindegyik shard egy kulon node-on el. Amikor beirod a keresest, mind az 5 node egyszerre keres a sajat shard-jaban, az eredmenyeket osszegyujti egy coordinator node, es visszaadja neked. Egy gepen ez 2 masodperc lenne, 5 gepen 400ms.

Masik pelda: Hadoop/Spark cluster. Egy telekommunikacios ceg naponta tobb TB hivasadatot general. Ezt egy Hadoop cluster-be toltik, ahol az adatot 20+ node-on taroljak elosztva (HDFS). Amikor elemzest futtatnak, a Spark job minden node-on lokalisan dolgozza fel a sajat adatszeletat, es csak az eredmenyt kuldi vissza. Nem kell az egesz adatot egy helyre mozgatni.

## Fo tanulsagok

**Mikor melyiket:**
**Infra/DevOps cluster** -- amikor az app nem allhat le, vagy egy szerver nem birja a forgalmat. Ha "0 downtime kell" vagy "lassan tolt be mert sokan hasznaljak", ez kell. SME-knel ritkan, de ha SLA-t igerunk (pl. 99.9% uptime), akkor igen.

**DB cluster** -- amikor az adatvesztes elfogadhatatlan, vagy az olvasasi terheles nagy. Ha kritikus az adatbazis (pl. rendeleskezelo, penzugyi rendszer), legalabb egy primary + replica felallas kell. A legegyszerubb ut: managed DB szolgaltatas, ne magad cluster-ezz.

**HPC cluster** -- valoszinuleg nem relevans. Ez kutatas, ML training, szimulációk.

**Elosztott adat cluster** -- amikor az adatmennyiseg kinovi az egy gepet, vagy a keresesnek gyorsnak kell lennie nagy adaton. Ha par ezer rekord van, PostgreSQL fulltext search eleg. Ha millios nagyságrendu adaton kell keresni, akkor jon az Elasticsearch. A legtobb projektnél nem kell.

**Gyakorlati szabaly:** az esetek 90%-aban egyetlen szerver + rendszeres backup + gyors ujratelepitesi terv elegendo. A cluster overhead-et (koltseg, komplexitas, karbantartas) csak akkor vallald be, ha van ra konkret uzleti indok.

## Kapcsolodo anyagok

- [[cloud/docker-compose|Docker Compose]]
- Kubernetes gyakorlat OrbStack -- cluster koncepcio a gyakorlatban, OrbStack egyetlen node-os klaszterrel
