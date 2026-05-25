# System Context — cic-yang (AI számára)

Olvasd el mielőtt bármit módosítasz.

---

## Mi ez a rendszer?

A `cic-yang` a CentralInfraCore YANG building block rétege — az a szint, amelyből
adapter sémák kompozícióval épülnek. Nem domain modell, nem adapter implementáció.

**Rétegdiagram — hol van a cic-yang a láncban:**

```
base-repo               ← tooling sablon (Makefile, CI, compiler)
    └──► cic-primitives ← irreducibilis szemantikai atomok + aggregátumok
              │           (Shape, Role, Behavior, Contract, Address,
              │            Identity, Event, Access — 8 atom)
              │           kind: AtomicPrimitive | AggregatePrimitive
              │
              └──► cic-yang  ← YANG adaptation layer
                        │      NEM DomainComposition — ez közbenső szint
                        │      kind: YANGBlock (saját, primitives enum-on kívül)
                        │      RFC 8343 / RFC 8344 / RFC 8516 building blockok
                        │
                        └──► cic-network v2  ← DomainComposition fogyasztó
                                  │             cic-yang blokkokat kompozícióz
                                  │             domain séma (NetworkInterface)
                                  │
                                  └──► adapter sémák  ← konkrét implementáció
                                        (pl. switch-netconf-adapter)
                                        building block kompozíció = implicit conformance
```

**Kulcsdisztinkció:** A cic-yang `kind: YANGBlock` — ez szándékosan nem szerepel
a primitives `index.yaml` enum-jában (`AtomicPrimitive|AggregatePrimitive|DomainComposition`).
A cic-yang adaptation layer, nem domain szint. Domain szint a cic-network és társai.

---

## Miért kell ez a réteg?

A hálózati domain tervezési tapasztalat (L-001) megmutatta: ha a domain séma maga
interpretálja az RFC-eket, minden domain repóban divergálnak. A cic-yang:
- Egyszer definiálja az RFC/OpenConfig interpretációt
- Verziózott: ha az értelmezés változik, egy helyen frissül
- Újrafelhasználható: cic-network v2, jövőbeli domain repók mind kompozíciózzák

---

## YANG scope — v1 (8 building block, v0.1.2)

| Modul | Forrás | Lefed | Státusz |
|---|---|---|---|
| `ietf-interfaces-base` | RFC 8343 | Közös interfész alap (type, enabled, l2-mtu) | **draft** |
| `ietf-interfaces-physical` | RFC 8343 | Fizikai interfész (ethernetCsmacd, LAG) | **draft** |
| `ietf-interfaces-logical` | RFC 8343 | Logikai interfész (loopback, bridge, bond) | **draft** |
| `ietf-interfaces-vlan` | RFC 8343 | VLAN szegmens (l2vlan, access/trunk/hybrid) | **draft** |
| `ietf-interfaces-tunnel` | RFC 8343 | Tunnel (VXLAN, GRE, IPIP) | **draft** |
| `ietf-ip-v4` | RFC 8344 | IPv4 konfiguráció (netplan-derivált) | **draft** |
| `ietf-ip-v6` | RFC 8344 | IPv6 konfiguráció (netplan-derivált) | **draft** |
| `ietf-lldp` | RFC 8516 | LLDP discovery (cherry-pick: D-005) | **draft** |

**Kizárva (service réteg — TILOS building block szintű felvétel):**
- RFC 8349 (routing protokollok) → service réteg
- RFC 8519 (ACL) → service réteg

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

## Jelenlegi állapot (2026-05-25)

| Elem | Státusz | Megjegyzés |
|---|---|---|
| git bootstrap + primitives/@v0.1.5 merge | **defined** | yang/devel branch bevezetve |
| project.yaml + dependency.yaml | **defined** | primitives/@v0.1.5 base |
| YANGBlock kind az index.yaml-ban | **defined** | D-001 — saját kind, nem primitives enum |
| 8 building block skeleton | **defined** | ietf-interfaces-base + ietf-lldp hozzáadva |
| Building block tartalom (mezők) | **draft** | fields stub-ok, tartalmi kitöltés még szükséges |
| `make validate` zöld | **defined** | Docker-alapú tooling |
| Developer pledge (commitment.yaml) | **defined** | createdBy + validity + Vault sign |
| Első signed release — yang/@v0.1.2 | **defined** | ECDSA + cic_countersign (CICSourceCA) |
| yang/devel branch | **defined** | branch rule: CLAUDE.md-ben rögzítve |
