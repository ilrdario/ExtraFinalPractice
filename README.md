# APSC 160: Final Exam Self-Study Practice Problems

---

## Part 1: Coding Challenges

### Question 1: Carbon Footprint Calculator (`track_grid_emissions`)
**Context:** Industrial servers running engineering simulations consume electricity from a grid whose carbon intensity varies over time. You need to calculate the total greenhouse gas emissions of a simulation run.

* **Signature:** `def track_grid_emissions(readings: list[tuple[float, float]], device_wattage: float) -> float:`
* **Preconditions:**
  * `device_wattage > 0` (in Watts).
  * Each tuple in `readings` is of the form `(duration_hours, grid_intensity)`, where `duration_hours >= 0` and `grid_intensity >= 0` (in grams of $CO_2$ per kilowatt-hour, $g/kWh$).
* **Postconditions:**
  * Returns the total carbon footprint in grams ($g$) of $CO_2$.
  * If the input `readings` list is empty, returns `0.0`.

#### Starter Code & Smoke Tests
```python
def track_grid_emissions(readings: list[tuple[float, float]], device_wattage: float) -> float:
    pass

if __name__ == "__main__":
    # Server consumes 200W
    # Slot 1: 2.5 hours at 50 g/kWh -> 0.5 kWh * 50 g/kWh = 25.0 g CO2
    # Slot 2: 1.0 hour at 400 g/kWh -> 0.2 kWh * 400 g/kWh = 80.0 g CO2
    # Total expected: 105.0 g CO2
    assert abs(track_grid_emissions([(2.5, 50.0), (1.0, 400.0)], 200.0) - 105.0) < 1e-6
    assert abs(track_grid_emissions([], 250.0) - 0.0) < 1e-6
    print("track_grid_emissions smoke tests passed!")
```

---

### Question 2: Geothermal Sensor Fusion (`merge_sensor_data`)
**Context:** Redundant temperature sensors are placed at geothermal heat pump wells to ensure reading reliability. To merge their reports, you must combine readings from two independent data nodes.

* **Signature:** `def merge_sensor_data(sensor1: dict[str, float], sensor2: dict[str, float]) -> dict[str, float]:`
* **Preconditions:**
  * Inputs are dictionaries mapping sensor node IDs (strings) to temperature readings (floats).
* **Postconditions:**
  * Returns a merged dictionary containing all unique sensor IDs as keys.
  * If a sensor ID exists in both dictionaries, its value in the merged dictionary must be the **average** of the two readings.
  * If a sensor ID exists in only one dictionary, its value is copied directly.
  * Does **not** mutate the input dictionaries (it must return a new dictionary).

#### Starter Code & Smoke Tests
```python
def merge_sensor_data(sensor1: dict[str, float], sensor2: dict[str, float]) -> dict[str, float]:
    pass

if __name__ == "__main__":
    s1 = {"well_A": 15.4, "well_B": 18.2}
    s2 = {"well_B": 19.0, "well_C": 12.8}
    
    expected = {"well_A": 15.4, "well_B": 18.6, "well_C": 12.8}
    result = merge_sensor_data(s1, s2)
    
    assert len(result) == 3
    assert abs(result["well_A"] - 15.4) < 1e-6
    assert abs(result["well_B"] - 18.6) < 1e-6
    assert abs(result["well_C"] - 12.8) < 1e-6
    assert s1 == {"well_A": 15.4, "well_B": 18.2}
    print("merge_sensor_data smoke tests passed!")
```

---

### Question 3: Scheduling Clean Compute (`find_cleanest_window`)
**Context:** High-performance computing workloads should be scheduled when the power grid is cleanest. Given an array of hourly grid carbon intensity forecasts, find the best consecutive block of hours to run a batch job.

* **Signature:** `def find_cleanest_window(intensities: list[float], window_size: int) -> int:`
* **Preconditions:**
  * `len(intensities) >= window_size >= 1`
  * All items in `intensities` are non-negative floats representing $g/kWh$ carbon emissions.
* **Postconditions:**
  * Returns the **starting index** of the consecutive window of length `window_size` that has the lowest average carbon intensity (which is equivalent to the lowest sum of intensity values).
  * If there is a tie, returns the **earliest starting index** (smallest index).

#### Starter Code & Smoke Tests
```python
def find_cleanest_window(intensities: list[float], window_size: int) -> int:
    pass

if __name__ == "__main__":
    forecast = [120.0, 110.0, 95.0, 90.0, 105.0, 85.0, 130.0]
    assert find_cleanest_window(forecast, 3) == 3
    
    forecast2 = [100.0, 50.0, 50.0, 100.0, 50.0, 50.0]
    assert find_cleanest_window(forecast2, 2) == 1
    print("find_cleanest_window smoke tests passed!")
```

---

### Question 4: Device Energy Logger (`aggregate_device_runs`)
**Context:** An automation system monitors various electrical devices in a manufacturing plant. It logs execution events as lightweight records.

* **Definition of Record:**
  ```python
  from collections import namedtuple
  RunRecord = namedtuple('RunRecord', ['device_id', 'duration_seconds', 'energy_source'])
  ```
* **Signature:** `def aggregate_device_runs(records: list[RunRecord]) -> dict[str, float]:`
* **Preconditions:**
  * `records` is a list of `RunRecord` instances.
* **Postconditions:**
  * Returns a dictionary mapping each unique `device_id` (string) to its cumulative run time in seconds (float).
  * If the input list is empty, returns an empty dictionary.

#### Starter Code & Smoke Tests
```python
from collections import namedtuple

RunRecord = namedtuple('RunRecord', ['device_id', 'duration_seconds', 'energy_source'])

def aggregate_device_runs(records: list[RunRecord]) -> dict[str, float]:
    pass

if __name__ == "__main__":
    logs = [
        RunRecord("pump_1", 120.0, "grid"),
        RunRecord("heater_2", 300.0, "solar"),
        RunRecord("pump_1", 45.0, "battery"),
        RunRecord("heater_2", 60.0, "grid")
    ]
    expected = {"pump_1": 165.0, "heater_2": 360.0}
    assert aggregate_device_runs(logs) == expected
    assert aggregate_device_runs([]) == {}
    print("aggregate_device_runs smoke tests passed!")
```

---

### Question 5: Transmission Losses in Hierarchical Grid (`total_power_output`)
**Context:** A smart power distribution network has a hierarchical, branching tree structure. Substations feed sub-grids, which in turn feed smaller nodes. We represent this network as a nested list where each element is either a float (power in $kW$ consumed/produced at a local node) or a sub-list (representing a downstream branch of the grid).

* **Signature:** `def total_power_output(grid: list) -> float:`
* **Preconditions:**
  * `grid` is a list that can contain floats (representing power values) or other lists recursively structured in the same way.
* **Postconditions:**
  * Returns the total sum of all power values found in the hierarchy.
  * If a list is empty or contains no floats, returns `0.0`.

#### Starter Code & Smoke Tests
```python
def total_power_output(grid: list) -> float:
    pass

if __name__ == "__main__":
    test_grid = [10.5, [5.0, [3.2]], 8.0]
    assert abs(total_power_output(test_grid) - 26.7) < 1e-6
    assert abs(total_power_output([]) - 0.0) < 1e-6
    print("total_power_output smoke tests passed!")
```

---

### Question 6: Grid Load Forecaster with Memoisation (`grid_load_forecast`)
**Context:** You are modeling daily peak load growth $L(n)$ in megawatts ($MW$) using a recurrence relation:
$$L(n) = L(n-1) + 2 L(n-2) \quad \text{for } n \ge 2$$
With base cases:
$$L(0) = 10.0 \quad \text{and} \quad L(1) = 15.0$$
If implemented naively, this calculation scales exponentially ($O(2^n)$), wasting immense computation energy. You must write an efficient version using recursion and **memoisation** to run in $O(n)$ time.

* **Signature:** `def grid_load_forecast(n: int, memo: dict[int, float] = None) -> float:`
* **Preconditions:**
  * `n >= 0`
* **Postconditions:**
  * Returns the forecasted load on day `n`.
  * Computes the result using memoisation. You must pass the `memo` dictionary through recursive calls.

#### Starter Code & Smoke Tests
```python
def grid_load_forecast(n: int, memo: dict[int, float] = None) -> float:
    pass

if __name__ == "__main__":
    assert abs(grid_load_forecast(0) - 10.0) < 1e-6
    assert abs(grid_load_forecast(1) - 15.0) < 1e-6
    assert abs(grid_load_forecast(2) - 35.0) < 1e-6
    assert abs(grid_load_forecast(4) - 135.0) < 1e-6
    
    # Memoisation verification: computing L(40) should be instantaneous
    assert grid_load_forecast(40) > 0
    print("grid_load_forecast smoke tests passed!")
```

---

### Question 7 (Hard): EV Charging Route Optimizer (`find_lowest_emissions_route`)
**Context:** An electric vehicle routing algorithm evaluates multiple possible routes. Each route is split into sequential 1-hour charging/driving segments. Each segment has an energy consumption value and takes place in a specific regional grid zone. Grid carbon intensity varies dynamically hour-by-hour.

* **Signature:** `def find_lowest_emissions_route(routes: dict[str, list[dict]], intensities: dict[str, dict[int, float]], start_hour: int) -> str:`
* **Preconditions:**
  * `routes` is a dictionary where each key is a route name (str) and the value is a list of segments. Each segment is a dict: `{"grid_zone": str, "energy_needed_kwh": float}`.
  * Segment $i$ (0-indexed) of a route takes place at hour `start_hour + i`.
  * `intensities` is a nested dictionary mapping a `grid_zone` (str) to a sub-dictionary. This sub-dictionary maps an hour (int) to the grid's carbon intensity (float, in $g\text{ }CO_2\text{/kWh}$).
  * `start_hour >= 0`.
  * All grid zones referenced in `routes` are guaranteed to exist in `intensities` for all travel hours.
  * `routes` contains at least one route, and each route contains at least one segment.
* **Postconditions:**
  * Returns the name of the route that results in the **lowest total emissions** (in grams of $CO_2$).
  * Total emissions for a route are calculated as the sum of emissions for each segment. Emissions for segment $i$ of a route are:
    $$\text{energy\_needed\_kwh} \times \text{intensity at } (\text{start\_hour} + i)$$
  * In the event of a tie in total emissions, return the route name that appears first alphabetically.

#### Starter Code & Smoke Tests
```python
def find_lowest_emissions_route(routes: dict[str, list[dict]], intensities: dict[str, dict[int, float]], start_hour: int) -> str:
    pass

if __name__ == "__main__":
    # Intensity data: grid zones 'Zone_A' and 'Zone_B'
    # Maps zone -> {hour -> g_CO2_per_kWh}
    intensity_forecast = {
        "Zone_A": {0: 100.0, 1: 150.0, 2: 200.0},
        "Zone_B": {0: 50.0, 1: 300.0, 2: 100.0}
    }
    
    # Route Options
    route_choices = {
        "Route_1": [
            {"grid_zone": "Zone_A", "energy_needed_kwh": 10.0},  # Hour 0: 10 kWh * 100 = 1000g
            {"grid_zone": "Zone_B", "energy_needed_kwh": 5.0}    # Hour 1: 5 kWh * 300 = 1500g -> Total = 2500g
        ],
        "Route_2": [
            {"grid_zone": "Zone_B", "energy_needed_kwh": 15.0},  # Hour 0: 15 kWh * 50 = 750g
            {"grid_zone": "Zone_B", "energy_needed_kwh": 10.0}   # Hour 1: 10 kWh * 300 = 3000g -> Total = 3750g
        ],
        "Route_3": [
            {"grid_zone": "Zone_A", "energy_needed_kwh": 8.0},   # Hour 0: 8 kWh * 100 = 800g
            {"grid_zone": "Zone_A", "energy_needed_kwh": 8.0}    # Hour 1: 8 kWh * 150 = 1200g -> Total = 2000g (Cleanest!)
        ]
    }
    
    assert find_lowest_emissions_route(route_choices, intensity_forecast, 0) == "Route_3"
    print("find_lowest_emissions_route smoke tests passed!")
```

---

### Question 8 (Hard): Microgrid Leak Detector (`find_leak_node`)
**Context:** A tree-structured microgrid distributes power to downstream nodes. Due to physical resistance, some transmission loss occurs, but a drop in power exceeding a specific threshold indicates an anomalous power leak. The network is represented recursively: a node is a dictionary containing its name, input power, output power, and a list of subcomponent nodes.

* **Signature:** `def find_leak_node(grid_node: dict, tolerance: float) -> str | None:`
* **Preconditions:**
  * `grid_node` is a dictionary containing:
    * `"name"` (str)
    * `"input_power"` (float, in $kW$)
    * `"output_power"` (float, in $kW$)
    * `"subcomponents"` (list of dicts, where each dict has the same structure as `grid_node`).
  * `tolerance >= 0` (in $kW$).
* **Postconditions:**
  * A node is considered to have a leak if its input power exceeds its output power by strictly more than `tolerance`:
    $$\text{input\_power} - \text{output\_power} > \text{tolerance}$$
  * Returns the `"name"` of the **first** node in the tree structure that exhibits a leak, searching in a **depth-first** manner (searching downstream subcomponents before checking parent nodes).
  * If no node in the tree has a leak, returns `None`.

#### Starter Code & Smoke Tests
```python
def find_leak_node(grid_node: dict, tolerance: float) -> str | None:
    pass

if __name__ == "__main__":
    # Tree representation:
    # Root: 'substation' (input=100.0, output=98.0) -> Loss = 2.0. No leak if tolerance=3.0.
    #   |-- Child 1: 'transformer_A' (input=45.0, output=44.5) -> Loss = 0.5.
    #   |-- Child 2: 'transformer_B' (input=50.0, output=45.0) -> Loss = 5.0. Leaking! (5.0 > 3.0)
    
    t_a = {"name": "transformer_A", "input_power": 45.0, "output_power": 44.5, "subcomponents": []}
    t_b = {"name": "transformer_B", "input_power": 50.0, "output_power": 45.0, "subcomponents": []}
    root = {"name": "substation", "input_power": 100.0, "output_power": 98.0, "subcomponents": [t_a, t_b]}
    
    # With tolerance 3.0, transformer_B leaks.
    assert find_leak_node(root, 3.0) == "transformer_B"
    
    # With tolerance 6.0, no component leaks.
    assert find_leak_node(root, 6.0) is None
    print("find_leak_node smoke tests passed!")
```

---

## Part 2: Tracing & Conceptual Questions

### Question 9: Memory Model, Aliasing, and Mutability
Consider the following Python code snippet:

```python
def process_readings(sensor_values: list[float], limit: float) -> list[float]:
    filtered = sensor_values
    for i in range(len(filtered)):
        if filtered[i] > limit:
            filtered[i] = limit
    filtered = filtered + [0.0]
    return filtered

original = [12.5, 9.8, 14.2]
cutoff = 10.0
result = process_readings(original, cutoff)
```

1. After the code runs, what are the values of the lists bound to the names `original` and `result`?
2. What are the results of the identity comparisons `original is result` and `filtered is result` (inside/outside the function scope)? Explain the difference between `==` and `is`.
3. Identify which operations in `process_readings` mutate an existing object in memory and which create a new object.
4. Draw or describe the stack frames and heap layout at the moment the execution reaches the line `filtered = filtered + [0.0]`.

---

### Question 10: Aliasing
Consider the following Python script:

```python
def update_grid(grid: list[dict], index: int, new_node: dict) -> None:
    grid[index] = new_node
    grid = grid.copy()
    grid[0]["active"] = False

sub = {"id": "sub_1", "active": True}
power_grid = [sub, {"id": "sub_2", "active": True}]
update_grid(power_grid, 1, {"id": "sub_3", "active": True})
```

1. After this script runs, what is the value of `power_grid`?
2. What is the value of the dictionary bound to the name `sub`?
3. Explain step-by-step why the dictionary `sub` was mutated even though the code executed a shallow copy `grid = grid.copy()` before modifying the item at index `0`.
4. Draw or describe the stack frames and heap layout showing reference arrows at the moment the execution reaches `grid[0]["active"] = False`.

---

### Question 11: Algorithmic Complexity, Scaling, and Carbon Footprint
An engineer writes a script to perform a collision check algorithm on a set of structural components. The script uses a nested loop to check every component against every other component.
* For $N = 2,000$ components, the algorithm performs $N^2 = 4,000,000$ checks, taking **4.0 seconds** to run on a 40W laptop CPU.
* The script runs 1,000 times a day on a server located in Calgary, Alberta, where the electrical grid carbon intensity is $500 \text{ g } CO_2\text{/kWh}$.

1. If the dataset size grows to $N = 8,000$ components, estimate the runtime of the original $O(N^2)$ algorithm.
2. The engineer optimizes the algorithm using a spatial partitioning sweep-line technique, reducing the time complexity to $O(N \log_2 N)$. When tested, the optimized version takes **0.1 seconds** for $N = 2,000$ components. Estimate the runtime of this optimized version for $N = 8,000$ components. (Use $\log_2(2000) \approx 11$ and $\log_2(8000) \approx 13$).
3. Calculate the energy savings (in Joules and in Watt-hours) for a single run of the script with $N = 8,000$ components.
4. Estimate the total carbon emissions saved (in kilograms of $CO_2$) over one year for running this optimized algorithm 1,000 times daily on the Alberta grid. How does this compare if the server were hosted in Vancouver, BC (grid intensity $11 \text{ g } CO_2\text{/kWh}$)?

---

### Question 12: Recursion, Call Stack, and Stack Overflow
Consider the following recursive function:

```python
def compute_sequence(n: int) -> int:
    if n == 1:
        return 1
    return n * compute_sequence(n - 2)
```

1. Trace the function call `compute_sequence(5)`. Show the sequence of recursive calls and draw the call stack frames showing their parameters at the deepest point of recursion.
2. What value does `compute_sequence(5)` return?
3. What happens if you call `compute_sequence(6)`? Explain step-by-step why this is a bug and name the exception that Python raises.
4. How would you modify the code to prevent this bug?

---

### Question 13: Binary Representation and Floating-point Encoding
1. Convert the decimal number `13.375` into its binary representation.
2. Consider the following Python script:
   ```python
   x = 0.0
   while x != 1.0:
       x += 0.1
   ```
   Explain why this loop represents an **infinite loop** in Python. What is the fundamental issue with floating-point encoding (IEEE-754) that causes this behavior?
3. Write a correct conditional statement that checks if a float variable `val` is equivalent to `0.3` within a tolerance of `1e-6`.

---

### Question 14: Systems & Hardware Concepts
1. Compare the L1/L2/L3 caches, RAM, and SSD/Hard Drive in terms of access speed (latency) and storage capacity. Why doesn't a computer just use cache memory for everything?
2. Python is an *interpreted* language, whereas C++ is a *compiled* language. Explain the difference between these two execution models and why compiled code generally executes significantly faster than interpreted code.
3. In numerical analysis, why is it dangerous to subtract two floating-point numbers of very similar magnitude? What is this phenomenon called?