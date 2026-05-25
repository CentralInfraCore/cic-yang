# LLM Szabályok / LLM Rules

## Magyar

- Minden állításhoz jelöld meg: **defined** / **draft** / **concept**
- Ha a státuszt nem tudod fájl-szinten alátámasztani, ne mondd ki tényként
- Aggregate-ből indulj, soha nem domain objektumból (Pod, Switch, VM)
- A kompozíció mechanizmusa git — ne javasolj YAML override rules rendszert
- Ne találj ki új fogalmat ha egy meglévő (IaC, YANG szemantika, control loop) fedi
- Kimenet YAML-ban determinisztikus: rendezett kulcsok, schema-first
- Ha egy primitive-re nem tudod megmondani a YAML, API és runtime mappinget → nem lezárt
- MCP kérdéseknél: graph-first, ne snippet-first
- `schemas/atomic/` és `schemas/aggregate/` upstream primitive fájlok — ne módosítsd
- Az RFC a primér forrás — CIC eltérést DECISIONS.md-be dokumentálj
- RFC 8349 (routing) és RFC 8519 (ACL): service réteg, TILOS building blockot csinálni
- IPv4 és IPv6 mindig külön building block (D-002)
- Törlés az elsődleges conformance mechanizmus; D-012 csak kivétel (D-003)
- `make validate` — ha nem zöld, javíts, ne mentsd az állapotot

---

## English

- Tag every claim as: **defined** / **draft** / **concept**
- If you cannot back up the status at the file level, do not state it as fact
- Start from the aggregate, never from a domain object (Pod, Switch, VM)
- The composition mechanism is git — do not propose a YAML override rules system
- Do not invent a new concept if an existing one (IaC, YANG semantics, control loop) covers it
- YAML output must be deterministic: ordered keys, schema-first
- If you cannot state the YAML, API, and runtime mapping for a primitive → it is not closed
- For MCP queries: graph-first, not snippet-first
- `schemas/atomic/` and `schemas/aggregate/` are upstream primitive files — do not modify
- The RFC is the primary source — document any CIC deviation in DECISIONS.md
- RFC 8349 (routing) and RFC 8519 (ACL): service layer, creating a building block is FORBIDDEN
- IPv4 and IPv6 are always separate building blocks (D-002)
- Deletion is the primary conformance mechanism; D-012 is the exception only (D-003)
- `make validate` — if not green, fix it, do not save the state