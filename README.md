# 🚗 Vehicle Rental Management System

> **Your fleet, one terminal away:** add cars and vans, check what is available, rent a ride, and settle its estimated charge when it returns. 🛣️

This Python project is a command-line vehicle rental manager. It tracks a small fleet of cars and vans, records whether each vehicle is rented, calculates rental charges, and saves inventory to a CSV file.

It is also a practical object-oriented programming example. An abstract `Vehicle` class defines shared behavior, `Car` and `Van` implement their own pricing rules, custom exceptions describe rental problems, and `RentalManager` coordinates the fleet and its persistence.

> [!NOTE]
> This is an educational example. Its pricing and record-keeping are deliberately simple; it is not a production booking, billing, or payment system.

---

## 🗺️ Contents

- [✨ Features](#-features)
- [🧭 A rental journey](#-a-rental-journey)
- [🏛️ Project architecture](#️-project-architecture)
- [🧰 Requirements](#-requirements)
- [🚀 Getting started](#-getting-started)
- [🖥️ Menu guide](#️-menu-guide)
- [💰 Rental pricing](#-rental-pricing)
- [📄 CSV persistence](#-csv-persistence)
- [🧠 Concepts demonstrated](#-concepts-demonstrated)
- [⚠️ Current behavior and edge cases](#️-current-behavior-and-edge-cases)
- [🌱 Ideas for future improvements](#-ideas-for-future-improvements)
- [✅ Learning challenges](#-learning-challenges)
- [📜 License](#-license)

---

## ✨ Features

- 🚘 **Register cars** with a daily rate and seat count.
- 🚐 **Register vans** with a daily rate and cargo capacity.
- 🆔 **Identify each vehicle** by a unique vehicle ID.
- 📤 **Rent an available vehicle** to a named customer.
- 📥 **Return a rented vehicle** and calculate a charge for the days used.
- 💰 **Use different pricing rules** for cars and vans.
- 🔍 **Search the fleet** by vehicle ID.
- 📋 **Display available vehicles** or the entire fleet.
- 💾 **Save and reload fleet data** with a CSV file.
- 🧱 **Practice inheritance, abstraction, encapsulation, properties, and custom exceptions.**

---

## 🧭 A rental journey

A typical session could look like this:

1. 🚘 Choose **1** to add a car or **2** to add a van.
2. 📋 Choose **6** to see the vehicles ready to rent.
3. 📤 Choose **3**, enter a vehicle ID, customer name, and rental duration.
4. 🧾 Review the estimated charge printed by the program.
5. 📥 When the vehicle comes back, choose **4** and enter its ID and actual days used.
6. 💾 Choose **8** to save the fleet, or **9** to save and exit.

The fleet lives in memory while the program runs. It is loaded from `vehicles.csv` when `RentalManager` is created, if that file exists. Changes are written to disk only when you select **Save Records** or **Save & Exit**.

---

## 🏛️ Project architecture

### 🧯 Custom exceptions

The program defines exceptions for common rental operations:

- `VehicleNotFoundError` — the requested vehicle ID is not in the fleet.
- `VehicleUnavailableError` — the vehicle is already rented.
- `VehicleNotRentedError` — a return is requested for a vehicle not marked as rented.
- `DuplicateVehicleError` — an ID is already registered.
- `InvalidRentalDurationError` — the requested number of days is zero or negative.

The menu catches these known errors and displays them as rental operation messages.

### 🧩 `Vehicle`

The abstract base class contains shared vehicle properties: ID, make, model, daily rate, rental status, and renter name. It provides methods to rent and return a vehicle, and requires subclasses to implement:

- `calculate_rental(days)`
- `get_details()`

### 🚘 `Car`

A car adds a seat count and a fixed insurance fee of **₹200 per day**. Its rental total combines the daily rate and insurance for each rental day.

### 🚐 `Van`

A van adds cargo capacity in tons and a capacity surcharge of **₹350 per ton per day**. Its rental total combines the daily rate and the capacity surcharge for each day.

### 🗂️ `RentalManager`

The manager stores vehicles in a dictionary keyed by vehicle ID. It registers cars and vans, searches for vehicles, handles rent and return operations, prints fleet views, and loads or saves CSV records.

### 🖥️ `main()`

The main function creates a manager and runs the interactive menu loop. It reads user input, calls the requested manager method, and handles known operational and input errors.

---

## 🧰 Requirements

- 🐍 Python 3.9 or later is recommended because the code uses built-in generic annotations such as `dict[str, Vehicle]`.
- 📦 No third-party packages are required.
- 🔌 The program uses Python's standard-library `csv`, `os`, and `abc` modules.

---

## 🚀 Getting started

1. Save the code in a file, for example `vehicle_rental.py`.
2. Open a terminal in the directory containing the file.
3. Run:

   ```bash
   python vehicle_rental.py
   ```

   If your system uses `python3`, run:

   ```bash
   python3 vehicle_rental.py
   ```

The menu starts in the terminal. Enter a number from **1** to **9**, then follow the prompts.

---

## 🖥️ Menu guide

### 1️⃣ Add Car

Enter a vehicle ID, make, model, daily rate, and seat count. The ID must not already exist in the fleet.

### 2️⃣ Add Van

Enter a vehicle ID, make, model, daily rate, and cargo capacity in tons. The ID must be unique.

### 3️⃣ Rent Vehicle

Enter a vehicle ID, customer name, and rental duration in whole days. The vehicle is marked as rented and an estimated charge is displayed.

### 4️⃣ Return Vehicle & Settle Bill

Enter a vehicle ID and the actual days used. The program calculates the charge for that duration, marks the vehicle as available, and prints a settlement amount.

The program does not store the original rental date or duration, issue invoices, collect payments, or keep a rental history. The return amount is calculated from the days entered at return time.

### 5️⃣ Search Vehicle

Enter an exact vehicle ID to show its details and current status.

### 6️⃣ Display Available Vehicles

Lists only vehicles that are not currently rented.

### 7️⃣ Display Full Fleet

Lists every vehicle, including vehicles currently rented.

### 8️⃣ Save Records

Writes the full fleet to `vehicles.csv`, replacing the file's previous contents.

### 9️⃣ Save & Exit

Saves the fleet and closes the application.

---

## 💰 Rental pricing

The total depends on the vehicle type, the daily rate, and the number of days.

### 🚘 Car formula

```text
Car total = (daily rate × days) + (₹200 insurance × days)
```

For a car with a daily rate of ₹1,000 rented for 3 days:

```text
(₹1,000 × 3) + (₹200 × 3) = ₹3,600
```

### 🚐 Van formula

```text
Van total = (daily rate × days) + (cargo tons × ₹350 × days)
```

For a van with a daily rate of ₹1,500, a 2-ton capacity, and 3 rental days:

```text
(₹1,500 × 3) + (2 × ₹350 × 3) = ₹6,600
```

Both pricing methods round the result to two decimal places. Rental duration must be at least one day.

---

## 📄 CSV persistence

The default data file is `vehicles.csv`, created in the current working directory. It contains a header row and one row per vehicle:

```csv
type,id,make,model,rate,spec,is_rented,rented_by
Car,CAR101,Tata,Nexon,1000.0,5,False,
Van,VAN202,Force,Traveller,1500.0,2.0,True,Jordan Lee
```

The `type` column determines whether the file row becomes a `Car` or a `Van`. The `spec` column stores seats for cars and cargo tonnage for vans. The `is_rented` column restores rental status, and `rented_by` restores the renter's name.

The file is opened with UTF-8 encoding. If it does not exist when the program starts, the fleet begins empty. Saving rewrites the file with the current fleet.

---

## 🧠 Concepts demonstrated

### 🧩 Abstraction

`Vehicle` inherits from `ABC` and declares `calculate_rental()` and `get_details()` as abstract methods. Every concrete vehicle type must provide its own implementation.

### 🧬 Inheritance and polymorphism

`Car` and `Van` inherit shared identity, status, and rental behavior from `Vehicle`. They each implement their own price calculation and detail display. The manager can call `calculate_rental()` on either type without needing to know the pricing formula in advance.

### 🔐 Encapsulation and properties

The daily rate, rental status, renter name, and type-specific details are protected inside objects. Properties expose read access, while methods such as `rent_vehicle()` and `return_vehicle()` update related state together.

### 🗂️ Dictionary lookup

The manager stores each vehicle under its ID. This supports direct lookup and makes duplicate-ID checks straightforward.

### 🧯 Domain-specific errors

Custom exceptions identify meaningful failure cases, such as trying to rent an already rented vehicle. This makes the menu's error handling more descriptive than a generic failure message.

### 💽 Persistence

The manager turns vehicle objects into CSV rows when saving and rebuilds the correct subclass when loading.

---

## ⚠️ Current behavior and edge cases

This README describes the supplied code as written:

- 📅 A rental's start date and planned duration are not stored. The return flow asks for actual days used and calculates a fresh amount.
- 🧾 Charges are printed but no payment is processed and no rental or payment history is retained.
- ⚠️ In `rent_vehicle()`, the vehicle is marked rented **before** the duration is validated by `calculate_rental()`. If the user enters zero or a negative duration, the rental operation raises an error but the vehicle may remain marked as rented.
- ⚠️ In `return_vehicle()`, the fare is calculated before the code checks whether the vehicle is rented. A non-positive duration can therefore raise a duration error before the not-rented condition is reported.
- 💾 Saving occurs only when the user selects one of the save options.
- 📄 Loading into an already populated manager replaces entries with matching IDs and adds other rows. It does not explicitly clear the fleet first.
- 🧹 If a loading error occurs after some rows have been processed, those earlier rows may already have changed the in-memory fleet.
- 🔢 Vehicle IDs are compared exactly as entered. The program does not normalize spaces or letter case.
- 🧪 The code converts rates and specifications to numbers but does not enforce positive rates, positive seat counts, or positive cargo capacity.
- 💵 Rental calculations use floating-point values. For real billing, decimal currency handling and explicit rounding rules are preferable.
- 🧍 Customer names are checked for blank values when renting, but the name is not saved as a separate customer record.

---

## 🌱 Ideas for future improvements

- ✅ Validate the rental duration before changing the vehicle's status.
- 📅 Store reservation dates, planned return dates, and actual return dates.
- 🧾 Keep a rental history and generate a receipt for every completed rental.
- 💰 Use `decimal.Decimal` for monetary calculations and define a billing policy.
- 🧹 Validate rates, seat counts, cargo capacity, IDs, makes, and models.
- 🔍 Normalize vehicle IDs consistently before comparing them.
- 📂 Validate the whole CSV before applying loaded records to the fleet.
- 🛟 Save to a temporary file first, then replace the old file after a successful write.
- 🧪 Add tests for price formulas, duplicate IDs, invalid durations, and rent/return state transitions.
- 🧱 Separate the command-line interface, rental rules, and storage code into different modules.
- 🧑‍🤝‍🧑 Add customer records and customer rental history.
- 🗃️ Move to a database if the application needs multiple users or concurrent bookings.

---

## ✅ Learning challenges

1. ❓ Why can `Vehicle` not be instantiated directly?
2. 🧬 How do `Car` and `Van` provide different implementations of the same abstract methods?
3. 💰 Calculate the charge for a car at ₹850 per day for 4 days.
4. 🚐 Calculate the charge for a 1.5-ton van at ₹1,200 per day for 2 days.
5. ⚠️ What happens to the rental status if the rental duration is invalid?
6. 🛠️ How would you change `rent_vehicle()` so the duration is validated before the vehicle is marked as rented?
7. 📄 How would you test that saving and loading preserves vehicle type and rental status?
8. 🧾 What additional information would a useful rental receipt include?

A great first improvement is to validate the duration before changing vehicle state, then test that an invalid rental leaves the vehicle available. 🧪

---

## 📜 License

No license was included with the supplied code. Add a license file if you plan to share or redistribute this project.

