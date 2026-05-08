# Tervezési döntések — cic-yang

---

## D-001 — YANGBlock: új schema kind (2026-05-06)

**Döntés:** A cic-yang building blockok `kind: YANGBlock` típussal szerepelnek
az index.yaml-ban. Ez az AtomicPrimitive, AggregatePrimitive, DomainComposition és
AdapterContract mellett az ötödik kind a CIC schema rendszerben.

**Miért:** A YANG building block nem DomainComposition (nincs ManagedEntity alap),
nem AdapterContract (nem interface contract), és nem primitive (nem irreducibilis atom).
Önálló szintaktikai kategória kell amely jelzi: ez RFC/OpenConfig YANG modul CIC adaptációja.

**YANGBlock kötelező mezők:** `yang_module`, `yang_source`, `interface_types` (ahol értelmezett)

---

## D-002 — IPv4/IPv6 szétválasztás (2026-05-06)

**Döntés:** Az RFC 8344 két külön building block:
- `ietf-ip-v4.yaml` — IPv4 konfiguráció
- `ietf-ip-v6.yaml` — IPv6 konfiguráció

**Miért:** Egy csak-IPv4 adapter nem kénytelen a teljes IPv6 blokkot `not_implemented`-del
jelölni — egyszerűen nem kompozícióz `ietf-ip-v6`-ot. A törlés (nem D-012) az elsődleges
conformance mechanizmus.

---

## D-003 — Törlés mint elsődleges conformance (2026-05-06)

**Döntés:** Ha egy adapter nem támogat egy YANG funkciót, az adapter sémájából
egyszerűen kihagyja azt a building blockot. Nem `not_implemented` annotáció — törlés.

**Miért:** A fa struktúra (derived schema) automatikusan kezeli: ismeretlen kulcs write-ra
schema validation error-t dob. Ez erősebb és egyszerűbb mint explicit `not_implemented`.

**D-012 (Access atom conformance) alkalmazási köre:**
Csak akkor kell, ha a mező strukturálisan bent van a sémában (nem hagyható el),
de az adott eszköz implementáció nem kezeli. Ez ritka kivétel, nem alapeset.

---

## D-005 — ietf-lldp main branch gap (2026-05-08)

**Döntés szükséges** (nyitott, BACKLOG B-003)

**Helyzet:**
Az `ietf-lldp.yaml` building block (`55b118c`) a `yang/releases/v0.1.0` → `yang/releases/v0.1.1`
útvonalon keletkezett — közvetlenül a releases branch-en, nem `yang/main`-en.
A `yang/main` HEAD-je (`b20ce04`) az ietf-lldp commit elődje a lineáris historyn.
Következmény: `yang/main` nem tartalmazza az ietf-lldp fájlt, hiába van benne a v0.1.1 release-ben.

**Hatás:**
- `cic-network` séma és `switch-netconf-adapter` erre épít
- Ha cic-yang v0.1.2 fejlesztése `yang/main`-ről indul, ietf-lldp hiányozni fog
- A `cic-yang-block.schema.yaml` (B-004) és az ietf-lldp együtt kell a következő release-hez

**Opciók:**
1. **Cherry-pick** → `git cherry-pick 55b118c` az ietf-lldp commitot yang/main-re hozza (gyors)
2. **Újraírás** → ietf-lldp v2 yang/main-en a következő release részeként (tisztább, de több munka)
3. **Elfogadás** → az lldp mindig releases branch-en él, dokumentáltan (legkevesebb munka, de törékeny)

**Ajánlott:** cherry-pick (1) — az ietf-lldp implementáció kész és stabil, nincs ok újraírásra.

---

## D-004 — YANG scope v1 (2026-05-06)

**Döntés:** v1 scope: RFC 8343 (ietf-interfaces) + RFC 8344 (ietf-ip).

**Kizárva:**
- RFC 8349 (ietf-routing) → service réteg (routing protokollok)
- RFC 8519 (ietf-acl) → service réteg (policy)
- Statikus route → netplan `routes:` field, az IP building block része

**Jövőbeli bővítés:** OpenConfig modellek (openconfig-interfaces stb.) külön building
blockként, `oc-interfaces-*` névvel — nem RFC forrás, de azonos mechanizmus.
