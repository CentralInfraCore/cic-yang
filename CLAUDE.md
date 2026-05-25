# cic-yang — Claude kontextus

## Mi ez a rendszer

A `cic-yang` a CentralInfraCore **YANG building block rétege** — IETF és OpenConfig
YANG modellek CIC primitív rendszerbe illesztett, újrafelhasználható schema fragmentei.

Nem domain modell. Nem adapter implementáció. Nem nyers YANG másolat.

A building block: egy YANG modul egy jól körülhatárolt részének CIC-szintű,
validálható, verziózott reprezentációja — amelyből adapter sémák kompozícióval épülnek.

Részletes architektúra: `ai/SYSTEM_CONTEXT.md`
Tervezési döntések: `ai/DECISIONS.md`
Kötelező szabályok: `ai/MAINTENANCE_CONTRACT.md`

---

## Branch szabály — KÖTELEZŐ

**Érdemi fejlesztés kizárólag a `yang/devel` ágon történhet.**

- `yang/main` — csak merge fogad (yang/devel → yang/main), közvetlen commit tilos
- `yang/releases/v*` — kizárólag release tag célra
- `yang/devel` — ez az aktív fejlesztési ág

Ha nem `yang/devel`-en vagyunk: figyelmeztetés, és átváltás `yang/devel`-re mielőtt bármilyen
schema, kód vagy dokumentáció változtatás történik.

---

## Boot sequence — minden session elején

1. `mcp__cic-graph__kb_status` — KB elérhető és friss?
2. `ai/DECISIONS.md` — D-001/D-002 ismerete kötelező
3. `ai/SYSTEM_CONTEXT.md` — teljes YANG layer kontextus
4. `ai/MAINTENANCE_CONTRACT.md` — mit szabad, mit nem

---

## Háromszintű státusz

| Státusz | Jelentés |
|---|---|
| **defined** | YAML séma létezik, `make validate` zöld |
| **draft** | Design megvan, séma még nincs |
| **concept** | Megbeszélt, formálisan nem rögzítve |

---

## Aktuális séma állapot

| Building block | Forrás | Státusz |
|---|---|---|
| `ietf-interfaces-base.yaml` | RFC 8343 | **draft** |
| `ietf-interfaces-physical.yaml` | RFC 8343 | **draft** |
| `ietf-interfaces-logical.yaml` | RFC 8343 | **draft** |
| `ietf-interfaces-vlan.yaml` | RFC 8343 | **draft** |
| `ietf-interfaces-tunnel.yaml` | RFC 8343 | **draft** |
| `ietf-ip-v4.yaml` | RFC 8344 | **draft** |
| `ietf-ip-v6.yaml` | RFC 8344 | **draft** |
| `ietf-lldp.yaml` | RFC 8516 | **draft** |

---

## Kritikus döntések

**D-001:** YANGBlock kind — új schema típus az index.yaml-ban
**D-002:** IPv4/IPv6 külön building block (nem egyben)
**D-003:** Törlés az elsődleges conformance mechanizmus; D-012 csak kivétel

---

## Kompozíciós lánc

```
base-repo
    └──► cic-primitives (primitives/@v0.1.5)
              └──► cic-yang (ez a repo)
                        └──► domain repók (cic-network v2, stb.)
                                  └──► adapter sémák
```

---

## Mérce

```bash
make validate          # ha nem zöld, semmi sem kész
make release VERSION=  # signed artifact (Vault szükséges)
```
