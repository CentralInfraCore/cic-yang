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

## D-004 — YANG scope v1 (2026-05-06)

**Döntés:** v1 scope: RFC 8343 (ietf-interfaces) + RFC 8344 (ietf-ip).

**Kizárva:**
- RFC 8349 (ietf-routing) → service réteg (routing protokollok)
- RFC 8519 (ietf-acl) → service réteg (policy)
- Statikus route → netplan `routes:` field, az IP building block része

**Jövőbeli bővítés:** OpenConfig modellek (openconfig-interfaces stb.) külön building
blockként, `oc-interfaces-*` névvel — nem RFC forrás, de azonos mechanizmus.
