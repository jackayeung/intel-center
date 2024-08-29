# Creating the README.md content based on the provided details
readme_content = """
# Intel Energy Data Center Project

## Overview

This project focuses on analyzing energy production and consumption across various U.S. regions to assist Intel in selecting an optimal location for a new data center. The analysis considers factors such as energy availability, usage, and the reliance on renewable energy sources, with the goal of identifying regions that produce a surplus of energy and have strong renewable energy infrastructure.

## Problem Statement

Intel, a leading semiconductor manufacturing company, is planning to build a new data center. Energy availability and usage are critical factors in determining the best location for this data center. The key questions include:
- Which regions produce a surplus of energy, making them likely to offer energy at lower prices?
- Which regions rely more on renewable energy sources?

## Datasets

The analysis used three primary datasets:

- **`intel.energy_data`**: Contains daily energy production and consumption data across various U.S. regions, including fields such as balancing authority, date, region, time of energy generation, demand, net generation, and the contribution of various energy sources (e.g., petroleum, coal, hydropower, natural gas, nuclear, solar, wind).

- **`intel.power_plants`**: Provides general information about power plants in the U.S., detailing plant name, code, region, state, and primary technology used.

- **`intel.energy_by_plant`**: Contains total energy production data at the plant level for the year 2022, including energy type (renewable or fossil fuel) and total energy generated in megawatts (MW).

## SQL Analysis

### Energy Generation by Region

#### Total Energy Production by Region
To identify regions that are net energy producers, we calculated the total energy produced per region by subtracting the energy demand from the net generation.

```sql
SELECT region, SUM(net_generation - demand) AS total_energy
FROM intel.energy_data
GROUP BY region
ORDER BY total_energy DESC;
```

Result: The Mid-Atlantic region was identified as the region with the highest positive total energy, indicating it produces significantly more energy than it consumes.

Renewable Energy Production by Region
To focus on regions generating energy primarily from renewable sources, we calculated the total renewable energy production by summing up the energy produced from hydropower, wind, and solar sources.

```sql
SELECT region, SUM(hydropower + wind + solar) AS renewable_energy
FROM intel.energy_data
GROUP BY region
ORDER BY renewable_energy DESC;
```

Result: The Northwest and Texas regions emerged as the top producers of renewable energy.
Percentage of Renewable Energy by Region
To understand the reliance on renewable energy, we calculated the percentage of energy generated from renewable sources relative to the total energy generation.

```sql
SELECT region, 
       (SUM(hydropower + wind + solar) / SUM(net_generation)) * 100 AS renewable_percentage
FROM intel.energy_data
GROUP BY region
ORDER BY renewable_percentage DESC;
```

Result: California, while not leading in total renewable energy, has a higher percentage of its total energy coming from renewable sources compared to Texas.
Aggregating Power Plant Data
Counting Renewable Power Plants by Region
We joined the intel.power_plants and intel.energy_by_plant tables to assess the distribution and output of renewable power plants across regions.