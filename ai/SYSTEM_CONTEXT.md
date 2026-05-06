# System Context — cic-yang (AI számára)

Olvasd el mielőtt bármit módosítasz.

---

## Mi ez a rendszer?

A `cic-yang` a CentralInfraCore YANG building block rétege — az a szint, amelyből
adapter sémák kompozícióval épülnek. Nem domain modell, nem adapter implementáció.

**Helye a kompozíciós láncban:**
```
cic-primitives  ← irreducibilis szemantikai atomok (Shape, Role, Access, stb.)
cic-yang        ← YANG modellek CIC-be illesztve (RFC 8343, 8344, OpenConfig)
cic-network v2  ← domain séma, cic-yang building blockok felhasználásával
adapter sémák   ← cic-yang blokkok kompozíciója = implicit conformance
```

---

## Miért kell ez a réteg?

A hálózati domain tervezési tapasztalat (L-001) megmutatta: ha a domain séma maga
interpretálja az RFC-eket, minden domain repóban divergálnak. A cic-yang:
- Egyszer definiálja az RFC/OpenConfig interpretációt
- Verziózott: ha az értelmezés változik, egy helyen frissül
- Újrafelhasználható: cic-network v2, jövőbeli domain repók mind kompozíciózzák

---

## YANG scope — v1

| Modul | Forrás | Lefed |
|---|---|---|
| `ietf-interfaces-physical` | RFC 8343 | Fizikai interfész (ethernetCsmacd, LAG) |
| `ietf-interfaces-logical` | RFC 8343 | Logikai interfész (loopback, bridge, bond) |
| `ietf-interfaces-vlan` | RFC 8343 | VLAN szegmens (l2vlan, access/trunk/hybrid) |
| `ietf-interfaces-tunnel` | RFC 8343 | Tunnel (VXLAN, GRE, IPIP) |
| `ietf-ip-v4` | RFC 8344 | IPv4 konfiguráció (netplan-derivált) |
| `ietf-ip-v6` | RFC 8344 | IPv6 konfiguráció (netplan-derivált) |

**Kizárva (service réteg):**
- RFC 8349 (routing protokollok) → service
- RFC 8519 (ACL) → service

---

## Adapter conformance mechanizmus

Az adapter sémája cic-yang building blockok kompozíciója.

```
ovs-adapter =
  ietf-interfaces-logical +
  ietf-interfaces-tunnel  +
  ietf-ip-v4              +
  ietf-ip-v6

juniper-l2-adapter =
  ietf-interfaces-physical +
  ietf-interfaces-vlan
  (nincs ip → IPv4/IPv6 mezők nem léteznek → schema validation error write-ra)
```

**Két szintű conformance:**
1. Mező nincs az adapter sémájában → törlés → schema validation error (unknown key)
2. Mező bent van de eszköz nem kezeli → `conformance: not_implemented` (D-012)

---

## YANGBlock schema struktúra

Minden building block YAML fájl:
```yaml
metadata:
  name: ietf-interfaces-physical
  source: RFC 8343 — ietf-interfaces
  version: v0.0.dev
  owner: Gabor Zoltan Sinko

spec:
  kind: YANGBlock
  yang_module: ietf-interfaces
  yang_source: RFC 8343
  interface_types: [ethernetCsmacd, ieee8023adLag]

  fields:
    config: [...]    # RFC 8343 config tree mezők
    state:  [...]    # RFC 8343 state tree mezők
```

---

## Jelenlegi állapot (2026-05-06)

| Elem | Státusz |
|---|---|
| git bootstrap + primitives/@v0.1.2 merge | **defined** |
| project.yaml + dependency.yaml | **defined** |
| YANGBlock kind az index.yaml-ban | **defined** |
| 6 building block skeleton | **draft** |
| Building block tartalom (mezők) | **pending** |
| `make validate` zöld | **pending** |
| Első signed release | **concept** |
