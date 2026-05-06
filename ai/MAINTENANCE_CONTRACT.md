# AI Maintenance Contract — cic-yang

---

## Mit szabad

- `schemas/ietf/` és `schemas/openconfig/` building block fájlok módosítása
- `ai/DECISIONS.md` bővítése új döntéssel (D-NNN formátum, dátummal)
- `ai/PROMPTMAP.yaml` státusz frissítése
- Új building block hozzáadása (új YANG modul/RFC)

## Mit nem szabad

- `schemas/atomic/` és `schemas/aggregate/` módosítása — upstream (cic-primitives)
- `schemas/index.yaml` módosítása a YANGBlock kind-on kívül — upstream
- `tools/`, `mk/`, `Makefile` módosítása — upstream
- RFC scope bővítése service réteg felé (RFC 8349, RFC 8519) — D-004 tiltja
- `make validate` megkerülése

## Building block authoritás

Minden building block az eredeti RFC/OpenConfig specifikáció alapján készül.
Ha a CIC interpretáció eltér az RFC-től, azt `ai/DECISIONS.md`-ben dokumentálni kell.
Az RFC a primér forrás, a CIC adaptáció a másodlagos.

## Release folyamat

```bash
git checkout -b yang/releases/vX.Y.Z
export VAULT_ADDR="https://127.0.0.1:18200"
export VAULT_TOKEN=$(cat $XDG_RUNTIME_DIR/vault/sign-token)
export VAULT_SKIP_VERIFY=1
make release
git tag "yang/@vX.Y.Z"
git tag "cic-yang@X.Y.Z"
```
