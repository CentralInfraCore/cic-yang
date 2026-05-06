# Onboarding (AI) — cic-yang

## 1 perc alatt

- **Mi ez:** YANG building block réteg — RFC/OpenConfig → CIC schema fragmentek
- **Nem ez:** domain modell, adapter implementáció, nyers YANG másolat
- **Scope v1:** RFC 8343 (interfaces) + RFC 8344 (IPv4/IPv6)
- **Mérce:** `make validate` — ha nem zöld, semmi sem kész

## Mielőtt bármit írsz

1. `mcp__cic-graph__kb_status` — KB elérhető?
2. Olvasd: `ai/SYSTEM_CONTEXT.md`
3. Olvasd: `ai/DECISIONS.md` — D-001..D-004

## Ha gond van

Javasolj design-diffet (`ai/DECISIONS.md`-hez), ne térj el csendben.
Az RFC a primér autoritás — ha valami ellentmond, azt kell dokumentálni.
