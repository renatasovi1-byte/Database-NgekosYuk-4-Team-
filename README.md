# NgekosYuk: Boarding-House Rental System — Database Design (ERD & DFD)

System design documentation (Data Flow Diagrams and Entity Relationship Diagram) for **NgekosYuk**, a web application for searching, renting, and managing boarding houses (*kos*) around Universitas Negeri Semarang (UNNES).

This was a **team project (4 members)** for the Database course, D3 Applied Statistics and Computation, Universitas Negeri Semarang (2025). **My contribution: the DFD (levels 0 and 1) and the ERD.** The MySQL database and web application were implemented by my teammates from this design.



---

## Problem

Students and workers moving to the Sekaran, Kalisegoro, and Patemon area mostly find a boarding house by asking friends or walking from place to place, which is slow and gives incomplete information. Owners promote vacant rooms with signboards and word of mouth, which limits their reach. NgekosYuk connects owners and tenants in one system for listing, searching, renting, paying, and reviewing.

## System actors

| Actor | Role in the system |
|---|---|
| Tenant (*penyewa*) | Searches listings, rents a boarding house, uploads payment proof, writes reviews and ratings |
| Owner (*pemilik*) | Registers and manages listings (price, facilities, location), views tenants and rental reports |
| Manager / Admin | Verifies payments, manages users and listings, monitors rental reports |

## Data Flow Diagrams

### Level 0 (context diagram)

![DFD Level 0](diagrams/dfd-level-0.png)

The system sits between three external entities (tenant, owner, manager). Tenants search and rent, owners supply listing data, and the manager supervises data and payments.

### Level 1

![DFD Level 1](diagrams/dfd-level-1.png)

Main processes:

1. **Registration**: new users register as tenant or owner, and the data is validated and stored.
2. **Manage listing data**: owners enter name, address, facilities, price, and category.
3. **Search and view listings**: tenants browse available boarding houses.
4. **Rental**: a tenant books a listing, and the rental record is saved.
5. **Payment confirmation**: after the admin validates the booking, the tenant uploads proof of payment, which is archived as a transaction record.
6. **Review and rating**: tenants rate a listing after renting.
7. **Rental report**: the manager views summaries of rentals and revenue.

## Entity Relationship Diagram

![ERD](diagrams/erd.png)

Notation: Chen. Entities are rectangles, attributes are ovals, relationships are diamonds, and cardinality is marked on the connecting lines.

### Entities

| Entity | Main attributes |
|---|---|
| `penyewa_kos` (tenant) | name, contact, gender, ID card number |
| `pemilik_kos` (owner) | name, address, bank account number, ID card number |
| `manager` | name, account data |
| `kos` (boarding house) | name, address, price, facilities, category |
| `penyewaan_kos` (rental) | tenant, boarding house, start and end date, total price |
| `konfirmasi_pembayaran` (payment confirmation) | payment date, amount, proof of transfer, method, status |
| `ulasan_rating` (review) | review text, rating score, date |
| `laporan_penyewaan` (rental report) | report period, total rentals, total revenue, summary |

### Relationships and cardinality

| Relationship | Cardinality | Note |
|---|---|---|
| Owner **owns** boarding house | 1 : M | One owner can own several listings |
| Tenant **rents** boarding house | M : N | Resolved through the `penyewaan_kos` entity |
| Rental **requires** payment confirmation | 1 : 1 | Payment proof is checked per rental |
| Tenant **writes** review for boarding house | 1 : M | Reviews follow a completed rental |
| Boarding house **appears in** rental report | 1 : M | One listing appears in reports of different periods |
| Manager **monitors** rental report | 1 : M | Manager views reports across periods |

## Design vs. implementation

The team's implemented MySQL database consolidated the 8 designed entities into **4 tables**:

| Design (ERD) | Implemented table |
|---|---|
| `penyewa_kos`, `pemilik_kos`, `manager` | `users` (single table with a `Role`: Tenant, Owner, Admin) |
| `kos` | `kos` |
| `penyewaan_kos`, `konfirmasi_pembayaran` | `penyewaan` (payment tracked by a status field and a proof-of-payment file) |
| `ulasan_rating` | `ulasan` |
| `laporan_penyewaan` | not a table; reports are derived from rental data |

Merging the three user types into one table avoids duplicated account data. The trade-off is that payment details are less detailed than in the design.

## Possible improvements

- Add a `kamar` (room) entity, so availability is tracked per room rather than a counter on the listing.
- Move facilities into a separate table so that facility data is atomic (First Normal Form).
- Keep payments as their own entity if multiple payments per rental (instalments) are needed.
- Hash passwords and enforce unique emails at the database level.

## Repository structure

```
.
├── diagrams/
|   ├── Data Flow Diagram-Halaman-3.drawio.png    #DFD Level 1
│   ├── Data Flow Diagram-dfd level 0.drawio.png
│   ├── Data Flow Diagram-tabell.drawio.png
│   ├── ERD-ERD Ngekost Yuk.drawio.png
│   └── source/draw.io
└── README.md
```

## Tools

Diagrams drawn with **draw.io, VSCode, MySQL**. The implementation stack (by teammates) used PHP, MySQL, and XAMPP.

## Team

- Nurul Azizah Fithria Anni
- Dela Kurniawati
- **Sovi Renata Ortesi Khaelani** (DFD and ERD design)
- Dena Aulia

Supervisor: Muhammad Iqbal, S.Kom., M.Kom.
