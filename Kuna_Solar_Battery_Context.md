# Context for Kuna Data Center Solar and Battery Model

## Project overview

This project evaluates solar photovoltaic and battery-storage options for a hypothetical data center in Kuna, Idaho. The data center has a constant 1 MW electrical load. The goal is to compare several solar and battery sizes and identify a design that reduces annual grid electricity use and peak grid demand without producing excessive curtailed solar energy.

## System boundaries and assumptions

- The model uses hourly time steps for one year, or 8,760 hours.
- The data-center demand is constant at 1 MW, so the hourly load is 1 MWh.
- Solar sizes are 0.5, 1.0, and 1.5 MW.
- Battery sizes are 0, 2, and 4 MWh.
- Charging efficiency is 95 percent.
- Discharging efficiency is 95 percent.
- The battery operates between 10 and 90 percent state of charge.
- Each configuration begins at its minimum allowable state of charge.
- Battery power limits, equipment degradation, economics, and grid export are not included in this first model.
- Solar energy first serves the load, then charges the battery. Any remaining solar energy is curtailed.
- During a solar deficit, the battery discharges first and the grid supplies the remaining load.

## Solar data

The final project should use hourly NREL solar data for Kuna, Idaho. For the WS4 prototype, no NREL file is currently available, so the code may create a deterministic hourly proxy solar-capacity-factor profile using daylight and seasonal patterns. All prototype plots and results must be labeled as proxy results. The code should keep the solar-data step separate so the proxy can later be replaced with an NREL CSV without changing the energy-balance logic.

## Required calculations

1. Calculate the grid-only annual-energy baseline.
2. Sweep all combinations of the three solar sizes and three battery sizes.
3. For each system, reset the battery state of charge and annual totals.
4. For every hour, calculate solar generation and compare it with the 1 MWh load.
5. If solar exceeds the load, serve the load, charge the battery within its limits, and curtail the remaining solar.
6. If solar is below the load, discharge the battery within its limits and use grid energy for the remaining deficit.
7. Store annual grid energy, renewable-energy percentage, peak grid demand, and curtailed solar energy for every configuration.
8. Verify that state of charge remains within its limits and that grid and curtailed energy never become negative.

## Battery equations

For a solar surplus:

- available_battery_space = maximum_SOC - battery_SOC
- maximum_charge_input = available_battery_space / charge_efficiency
- energy_sent_to_battery = minimum(surplus_energy, maximum_charge_input)
- battery_SOC = battery_SOC + energy_sent_to_battery * charge_efficiency
- curtailed_energy = surplus_energy - energy_sent_to_battery

For a solar deficit:

- available_battery_energy = battery_SOC - minimum_SOC
- energy_removed_from_battery = minimum(available_battery_energy, deficit / discharge_efficiency)
- battery_energy_delivered = energy_removed_from_battery * discharge_efficiency
- battery_SOC = battery_SOC - energy_removed_from_battery
- grid_energy = deficit - battery_energy_delivered

## Required outputs

- A CSV table containing results for the grid-only baseline and all nine solar-and-battery configurations.
- A plot comparing annual grid electricity use by configuration.
- A plot comparing renewable-energy percentage by configuration.
- A printed summary of the configuration with the lowest annual grid use.

## Coding requirements

- Use Python and Matplotlib for the plots. The numeric model and CSV output should still run if Matplotlib is unavailable.
- Use clear variable names and comments that correspond to the pseudocode.
- Keep the hourly energy-balance logic easy to read.
- Do not add cost optimization or equipment models that are outside the stated scope.
- Save the comparison table as a CSV and the plots as a PNG in a clearly named output folder.

