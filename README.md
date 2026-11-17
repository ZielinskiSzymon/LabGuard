# 🛡️ LabGuard — System Rezerwacji Sprzętu i Zgłaszania Usterek w Pracowniach IT

## 1. Zgłoszenie pomysłu

### Nazwa i autorzy
* **Nazwa projektu:** LabGuard — Bezpieczny System Zarządzania Pracownią Techniczną i Wypożyczalnią Sprzętu
* **Autor:** Szymon Zieliński oraz mateusz Ogonek

### Cel aplikacji
* **Dla kogo:** Aplikacja tworzona jest dla uczniów oraz opiekunów pracowni informatycznych i sieciowych w technikum.
* **Jaki problem rozwiązuje:** Zastępuje papierowe zeszyty wypożyczeń, eliminuje konflikty terminów przy rezerwacji sprzętu laboratoryjnego (switchy Cisco, routerów, laptopów diagnostycznych, testerów sieciowych) oraz pozwala szybko zgłaszać usterki stanowisk komputerowych.
* **Przykład użycia:** Uczeń przygotowujący się do egzaminu zawodowego potrzebuje wypożyczyć przełącznik Cisco oraz zaciskarkę na piątkowe popołudnie. Loguje się do systemu, sprawdza dostępność i rezerwuje sprzęt. Opiekun pracowni zatwierdza wydanie sprzętu w swoim panelu.

### Planowane funkcje (Czynności użytkownika)
1. **Przeglądanie dostępnego sprzętu:** Uczeń widzi katalog urządzeń w pracowni wraz z numerami seryjnymi i statusem dostępności.
2. **Rezerwacja sprzętu na wybrany termin:** Uczeń wybiera sprzęt oraz przedział czasowy i zapisuje rezerwację.
3. **Podgląd i odwoływanie własnych rezerwacji:** Uczeń widzi swoje rezerwacje i może anulować wybraną rezerwację przed jej rozpoczęciem.
4. **Zgłaszanie usterek w pracowni:** Uczeń wypełnia formularz awarii stanowiska komputerowego (numer stanowiska, priorytet, opis).
5. **Zarządzanie rezerwacjami przez opiekuna:** Opiekun pracowni (Administrator) zatwierdza lub odrzuca wnioski uczniów.

### Technologie i dane
* **Interfejs użytkownika (Frontend):** React 18 + Vite oraz Bootstrap 5.
* **Obsługa danych (Backend):** Node.js z frameworkiem Express.js (REST API).
* **Baza danych:** Relacyjna baza danych SQLite (`sqlite3`) z zapytaniami parametryzowanymi (*Prepared Statements*).

### Zagrożenia i planowane zabezpieczenia
| Zagrożenie | Co ktoś mógłby zrobić bez uprawnień / jakie błędne dane przesłać? | Planowane zabezpieczenie |
| :--- | :--- | :--- |
| **IDOR (Odwołanie cudzej rezerwacji)** | Użytkownik A mógłby zmienić identyfikator rezerwacji w żądaniu `DELETE /api/reservations/:id` i anulować rezerwację Użytkownika B. | Kontrola dostępu po stronie serwera sprawdzająca, czy `reservation.user_id === req.user.id` na podstawie tokenu JWT. |
| **Błędne dane czasowe (Pominięcie formularza)** | Użytkownik mógłby ominąć walidację HTML i wysłać rezerwację z datą zakończenia wcześniejszą niż data rozpoczęcia. | Walidacja po stronie serwera sprawdzająca poprawność dat (`endDate > startDate`) oraz kolizje terminów w bazie. |
| **Wstrzyknięcie kodu HTML/JS (XSS)** | Użytkownik mógłby wpisać w opisie usterki `<b>Próba</b>` lub `<script>alert(1)</script>`, aby zmodyfikować wygląd strony lub wykraść sesję. | Neutralizacja znaczników HTML i bezpieczne renderowanie tekstu w React (znaczniki wyświetlą się dosłownie). |
| **Nieautoryzowane wywołanie funkcji opiekuna** | Zwykły uczeń mógłby wysłać żądanie zatwierdzenia własnej rezerwacji na adres `/api/admin/...`. | Middleware `requireAdmin` weryfikujący rolę użytkownika w tokenie JWT i blokujący dostęp kodem `403 Forbidden`. |

### Plan pracy
* **Etap 1:** Zgłoszenie pomysłu, przygotowanie repozytorium Git i dokumentacji początkowej.
* **Etap 2:** Przygotowanie bazy danych SQLite oraz pierwszej funkcji działającej od początku do końca (dodawanie i wyświetlanie rezerwacji).
* **Etap 3:** Dodanie logowania JWT, sprawdzania dostępności sprzętu oraz walidacji dat po stronie serwera.
* **Etap 4:** Wdrożenie kontroli dostępu (ochrona przed IDOR) oraz ochrony funkcji administratora (RBAC).
* **Etap 5:** Dodanie modułu zgłaszania usterek z ochroną przed XSS, wykonanie testów bezpieczeństwa i opisanie wyników w README.md.