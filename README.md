# Zadatak 24 – HTTP Server sa keširanjem i logovanjem

## Opis projekta

Ovaj projekat implementira jednostavan **konzolni HTTP server** koji obrađuje GET zahteve i koristi **višenitno programiranje** (ThreadPool) uz **lokalno keširanje odgovora** i **logovanje svih aktivnosti**.

---

### Šta server radi?

- Kada korisnik pošalje GET zahtev na rutu `/api/github/commits?owner=OWNER&repo=REPO`,
  - server asinhrono šalje zahtev GitHub API‑ju,
  - vraća JSON rezultat koji sadrži broj commitova u datom repozitorijumu,
  - rezultat kešira za buduće zahteve istog URL‑a.

### Primer zahteva:
```http
GET http://localhost:8080/api/github/commits?owner=dotnet&repo=runtime
```
```http
GET http://localhost:8080/api/github/commits?owner=dotnet&repo=runtime&since=2024-01-01&until=2024-01-31
```

```http
GET http://localhost:8080/api/github/commits?owner=dotnet&repo=runtime&anon=true
```

```http
GET http://localhost:8080/api/github/commits?owner=dotnet&repo=runtime&force=true
```

---

## Višenitno programiranje

- Server koristi **ThreadPool** da obradi zahteve istovremeno.
- `SemaphoreSlim` se koristi za ograničavanje broja paralelnih zahteva (`maxApiParallel = 4`).

---

## Keširanje

- Odgovori se keširaju u memoriji koristeći `InMemoryCache.cs` (bazirano na `ConcurrentDictionary`).
- Ako zahtev stigne ponovo sa istim URL‑om, vraća se keširan odgovor bez pozivanja GitHub API‑ja.

---

## Logovanje

- Logovi se čuvaju u direktorijumu `logs/` sa dnevnim fajlovima `log-yyyy-MM-dd.txt`.
- Loguju se:
  - vreme početka obrade,
  - vrednost upita,
  - da li je keš korišćen,
  - greške,
  - uspešni odgovori.

---

## Kako testirati?

1. Pokreni projekat (`Program.cs`).
2. Otvori browser.
3. Pošalji GET zahtev:
```http
http://localhost:8080/api/github/commits?owner=dotnet&repo=runtime
```
4. Rezultat će prikazati ukupan broj commitova.
5. Sledeći isti zahtev biće poslužen iz keša.

---

## Struktura projekta

- `Program.cs` – Ulazna tačka, startovanje servera.
- `HttpServer.cs` – Glavna logika za pokretanje HTTP listenera.
- `GitHubClient.cs` – Slanje zahteva GitHub API‑ju.
- `InMemoryCache.cs` – Implementacija keširanja.
- `RequestLogger.cs` – Logovanje svih događaja.
- `Utils.cs` – Pomoćne funkcije.

