# Airline-Management-System-with-PL-SQL



A relational database project for managing the operations of a commercial airline, built with **Oracle 19c** and **PL/SQL**. The system models flights, passengers, reservations, aircraft, airports, and airline staff.

---

## Project Overview

This database models the reservation and operations system of a commercial airline. It covers both the operational side (flights, aircraft, crew scheduling) and the customer-facing side (clients, reservations, passengers, loyalty accounts).

**Technology:** Oracle 19c · PL/SQL · Oracle SQL Developer  
**Platform:** Windows (no virtual machine)

---

## Database Schema

The schema consists of **16 tables** organized around the following entities:

| Table | Description |
|---|---|
| `CLIENT` | Registered customers of the airline |
| `CONT_CLIENT` | Loyalty accounts linked to clients (Bronze/Silver/Gold) |
| `PASAGER` | Passengers (not necessarily clients themselves) |
| `BAGAJ` | Luggage associated with each passenger |
| `ZBOR` | Flights with departure/arrival airports and aircraft |
| `REZERVARE` | Reservations linking a client, a flight, and a passenger |
| `BILET` | Tickets — confirms a passenger's seat on a flight |
| `ANGAJAT` | All employees (base entity) |
| `PILOT` | Pilots (ISA subtype of ANGAJAT) |
| `INSOTITOR_ZBOR` | Flight attendants (ISA subtype of ANGAJAT) |
| `CADRU_MEDICAL` | Medical staff (ISA subtype of ANGAJAT) |
| `ORAR_ANGAJATI` | Employee-flight schedule (role: PRINCIPAL / SECUNDAR) |
| `AEROPORT` | Airports |
| `ORAS` | Cities |
| `TARA` | Countries |
| `CONTINENT` | Continents |

### Key Business Rules

- A passenger is not required to be a client — a client can book on behalf of others.
- A client cannot make duplicate reservations for the same passenger on the same flight.
- Each passenger may have at most one piece of luggage.
- Each client may have at most one loyalty account.
- Flights are direct (no stopovers); routes are defined by exactly two airports.
- Employees cannot be scheduled on two overlapping flights.
- Every flight must have at least one pilot and one flight attendant.
- Reservations cannot be made for flights that have already departed.


---

## PL/SQL Components

### Stored Procedures & Functions

| Object | Type | Description |
|---|---|---|
| `detalii_zbor` | Procedure | Displays a flight summary: passenger list, ID document stats, and top 3 passengers by luggage cost. Uses all 3 collection types (nested table, index-by table, varray). |
| `raport_angajati` | Procedure | Generates an employee report with 3 filter options (all / salary range / hire year). Uses a dynamic cursor and a parameterized cursor. Returns total employees processed. |
| `f_top_client_continent` | Function | Returns the client with the most confirmed reservations for departures from a given continent. Joins 5 tables; handles `NO_DATA_FOUND`, `TOO_MANY_ROWS`, and custom exceptions. |
| `p_bonus_piloti_buget` | Procedure | Awards salary bonuses to eligible pilots (by aircraft type and hire year) within a given budget. Defines 5 custom exceptions; joins 5 tables in a single SQL statement. |

### Triggers

| Trigger | Type | Description |
|---|---|---|
| `trig_ang_salariu` | DML / Statement-level | Fires after `UPDATE` on employee salary; calls `raport_angajati` to log changes. |
| `trg_rezervare_auto` | DML / Row-level | Fires before `INSERT` or `UPDATE` on `REZERVARE`; automatically inserts a ticket into `BILET` when a reservation is confirmed. |
| `protect_continent` | DDL / Schema-level | Blocks `ALTER` or `DROP` on the `CONTINENT` table; logs blocked attempts to an audit table using `PRAGMA AUTONOMOUS_TRANSACTION`. |

### Package: `ECHIPAJ_ZBOR`

A package for generating a complete crew profile for a given flight.

**Types defined:** `t_membru_rec`, `t_membru_tab`, `t_stat_map`, `t_rc`, `t_profil_echipaj`

| Component | Kind | Description |
|---|---|---|
| `p_check_zbor` | Procedure | Validates that a flight code exists in the database |
| `f_ani_experienta` | Function | Calculates years of experience for an employee |
| `f_lista_echipaj` | Function | Returns a nested table of all crew members for a flight |
| `p_valideaza_echipaj` | Procedure | Validates that the crew includes at least one principal pilot |
| `f_scor_echipaj` | Function | Computes a numeric crew score based on experience and salary |
| `f_profil_echipaj` | Function | Returns a record with full crew statistics (counts, salary range, score) |
| `p_afiseaza_echipaj` | Procedure | Prints the full crew report for a flight |

---

## Sample Data

The database is seeded with realistic sample data including 15 clients, 14 loyalty accounts, 7 continents, 15 countries, 19 cities, 19 airports, 8 aircraft models, 14 scheduled flights, 15 passengers, 15 pieces of luggage, 15 employees (5 pilots, 5 flight attendants, 5 medical staff), and 28+ reservations.

  

