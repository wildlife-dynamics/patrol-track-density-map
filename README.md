# Patrol Track Density Map Workflow

## Introduction

This workflow helps you to visualize where your ranger patrols have concentrated their effort. It converts patrol tracks from **EarthRanger** into a gridded heatmap, where each grid cell is colored by how much patrol effort (time spent, distance travelled, or normalised time density) occurred inside it — making it easy to spot well-covered areas and coverage gaps at a glance.

**What this workflow does:**
- Downloads patrols and their GPS observations from **EarthRanger** for your chosen time range and patrol type(s)
- Converts patrol observations into track segments, with optional filtering to remove bad GPS fixes and implausible movement
- Divides the patrolled area into a grid and calculates patrol effort per grid cell, weighted by **Time**, **Distance**, or **Normalised (LTD)** percentiles
- Creates an interactive density map with a color-coded legend (green = low effort, red = high effort)
- Optionally splits the map into separate views by category (patrol type, serial number, subject) or time period (month, year, etc.)

**Who should use this:**
- Protected area and conservation managers assessing ranger patrol coverage
- Ecologists and analysts studying patrol effort distribution and coverage gaps
- Anyone needing to visualize patrol track density from data stored in EarthRanger

## Prerequisites

Before using this workflow, you need:

1. **Ecoscope Desktop** installed on your computer
   - If you haven't installed it yet, please follow the installation instructions for Ecoscope Desktop

2. **EarthRanger Data Source** configured in Ecoscope Desktop
   - You must have already set up a connection to your EarthRanger server
   - Your data source should be configured with proper authentication credentials
   - You'll need to know the name of your configured data source (e.g., "mep_dev")

3. **Patrols with Patrol Types** set up in EarthRanger
   - You need patrols recorded in your EarthRanger system during the time period you want to analyze
   - You'll need to know the patrol type(s) you want to analyze (e.g., "ecoscope_patrol")
   - You can find your patrol types in your EarthRanger Admin site under **Activity → Patrol Types** at `https://<your-site>.pamdas.org/admin/activity/patroltype/`

## Installation

1. Select "Workflow Templates" tab
2. Click "+ Add Template"
3. Copy and paste this URL https://github.com/wildlife-dynamics/patrol-track-density-map and wait for the workflow template to be downloaded and initialized
4. The template will now appear in your available template list

## Configuration Guide

### Basic Configuration

#### 1. Workflow Details
Add information that will help to differentiate this workflow from another.

- **Workflow Name** (required): A descriptive name for this workflow run
  - Example: `"Patrol Track Density Map"`
- **Workflow Description** (optional): Additional details about this analysis
  - Example: `"Gridded density map of patrol tracks."`

#### 2. Data Source
Select one of your configured data sources.

- **Data Source** (required): The name of your configured EarthRanger connection
  - Example: `"mep_dev"`
  - Note: The data source must be configured in Ecoscope Desktop before you can select it

#### 3. Time Range
Choose the period of time to analyze.

- **Since** (required): The start time
  - Example: `2015-01-10T00:00:00`
- **Until** (required): The end time
  - Example: `2015-02-28T23:59:59`
- **Timezone** (optional): The timezone used to display times in your results
  - Example: `Africa/Nairobi (UTC+03:00)` or `UTC (UTC+00:00)`

#### 4. Patrol Types
Choose which patrols to include in the density map.

- **Patrol Types** (required field, can be left empty): Specify the patrol type(s) to analyze. Leave this section empty to analyze all patrol types.
  - Example: `"ecoscope_patrol"`
  - Note: If you are on Ecoscope Desktop, "Patrol Type" values can be found in your EarthRanger Admin site under Activity → Patrol Types. The dropdown options update based on your selected data source.

#### 5. Group Data
Configure how patrol tracks are grouped (by category or temporal index) and split into per-group dashboard views. Leave empty for a single combined map.

- **Group by** (optional): Add one or more groupers:
  - **Category**: Split the map by a patrol attribute
    - `Patrol Serial Number`: One map view per patrol
    - `Patrol Type`: One map view per patrol type
    - `Patrol Subject`: One map view per patrol subject (e.g., ranger team or tracked device)
  - **Time**: Split the map by time period
    - `Year (example: 2024)`, `Month (example: September)`, `Year and Month (example: 2023-01)`, `Day of the year as a number (example: 365)`, `Day of the month as a number (example: 31)`, `Day of the week (example: Sunday)`, `Hour (24-hour clock) as number (example: 22)`, or `Date (example: 2025-01-31)`

#### 6. Patrol Track Density Map
Configure how patrol effort is calculated for each grid cell.

- **Density Calculation** (optional, default: `Time`): Weight each grid cell by total patrol time or distance travelled, or by time normalised as a percentage of the total (LTD).
  - `Time`: Each cell shows the total time patrols spent inside it
  - `Distance`: Each cell shows the total distance patrols travelled inside it
  - `Normalised (LTD)`: Each cell shows its patrol time density as a percentile of the total — useful for comparing relative coverage rather than absolute effort
- **Percentile Levels** (only shown when `Normalised (LTD)` is selected): Choose the time density percentile bins to display.
  - Options: `50`, `60`, `70`, `80`, `90`, `95`, `99`, `99.999`, `100`
  - Default: `50, 60, 70, 80, 90, 100`

### Advanced Configuration

These optional settings provide additional control over your workflow:

#### Patrol Types — Advanced

- **Patrol Status** (optional, default: `done`): Choose to analyze patrols with a certain status (`active`, `overdue`, `done`, `cancelled`). If left empty, patrols of all status will be analyzed.
- **Patrols Overlap Daterange** (optional, default: enabled): Whether or not to include patrols that start or end outside of the time range.

#### Filter Data
Clean up the patrol tracks before the density is calculated. The defaults include all data worldwide and only remove clearly implausible movement.

- **Bounding Box**: Only include patrol observations whose coordinates fall inside this bounding box.
  - Fields: **Min Latitude** (default `-90`), **Max Latitude** (default `90`), **Min Longitude** (default `-180`), **Max Longitude** (default `180`)
- **Filter Exact Point Coordinates**: Exclude observations recorded at these exact coordinates (e.g. known bad GPS fixes).
  - Default: `(180, 90)`, `(0, 0)`, `(1, 1)` — common placeholder coordinates from GPS devices
- **Trajectory Filter**: Drop trajectory segments outside these length / time / speed bounds (e.g. to remove implausible jumps).
  - **Minimum/Maximum Segment Length (Meters)**: default `0.001` – `100000`
  - **Minimum/Maximum Segment Duration (Seconds)**: default `1` – `172800`
  - **Minimum/Maximum Segment Speed (Kilometers per Hour)**: default `0.01` – `500`

#### Map Base Layers
Select tile layers to use as base layers in map outputs. The first layer in the list will be the bottommost layer displayed.

- **Base Layer** options: `Open Street Map`, `Roadmap`, `Satellite`, `Terrain`, `USGS Hillshade`, or `Custom Layer (Advanced)` with your own tile URL
- **Layer Opacity**: Set layer transparency from 1 (fully visible) to 0 (hidden)
- Default: `Terrain` at opacity `1.0` with `Satellite` at opacity `0.5` layered on top

#### Patrol Track Density Map — Advanced

- **Heatmap Layer Opacity** (default: `0.7`): Set heatmap layer transparency from 1 (fully visible) to 0 (hidden). Lower values let you see the base map through the density grid.
- **Grid Cell Size** (default: `Auto-scale`): Auto-scale for an optimized grid cell size based on the workflow data, or Customize to set a specific grid cell size.
  - **Custom Grid Cell Size** (default: `5000`, must be below `10000`): Define the resolution of the raster grid (in the unit of measurement defined by the coordinate reference system set below). A smaller grid cell size provides more detail, while a larger size generalizes the data.
- **Coordinate Reference System** (default: `EPSG:3857`): The coordinate reference system in which to perform the calculation, must be a valid CRS authority code, for example `ESRI:53042`.

## Running the Workflow

Once you've configured all the settings:

1. **Review your configuration**
   - Double-check your time range, data source, and patrol types

2. **Save and run**
   - Click the "Submit" and the workflow will show up in "My Workflows" table button in Ecoscope Desktop
   - Click on "Run" and the workflow will begin processing

3. **Monitor progress and wait for completion**
   - You'll see status updates as the workflow runs
   - Processing time depends on:
     - The size of your date range
     - Number of patrols and patrol types included
     - Number of GPS observations recorded per patrol
   - The workflow completes with status "Success" or "Failed"

## Understanding Your Results

After the workflow completes successfully, open the dashboard to explore your results.

### Visual Outputs (Dashboard)

The workflow creates an interactive dashboard with one main visualization:

#### Patrol Track Density Map
- **Format**: Interactive map with a gridded heatmap overlay
- **Features**:
  - **Grid cells**: Each cell is colored by the patrol effort inside it, from green (low effort) through yellow to red (high effort)
  - **Legend**: Shows the density bins in the bottom-right corner; the legend title reflects your chosen density calculation (time, distance, or LTD percentiles)
  - **Interactive hover**: Shows the exact `Patrol Effort` value when you mouse over a grid cell
  - **North arrow**: Displayed in the top-left corner
  - **Base maps**: Your selected base layers (default: terrain with semi-transparent satellite imagery)
  - **Zoom and pan**: Fully interactive — zoom in to inspect individual grid cells

### Grouped Outputs

If you configured groupers in **Group Data**, the dashboard includes a filter control that lets you switch between per-group map views:

- **Category grouper** (e.g., Patrol Type): One density map per patrol type, labeled with the patrol type's display name from EarthRanger (e.g., "Wildlife Sighting")
- **Time grouper** (e.g., Month): One density map per time period (e.g., January, February)
- **Both**: One map per combination (e.g., each patrol serial number in each month)

## Common Use Cases & Examples

Here are some typical scenarios and how to configure the workflow for each:

### Example 1: Where did patrols spend their time?
**Goal**: See which areas received the most patrol time over a two-month period

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `Africa/Nairobi (UTC+03:00)`
- **Patrol Types**: `"ecoscope_patrol"`
- **Density Calculation**: `Time`

**Result**:
- A single density map where each grid cell is colored by the total time patrols spent inside it
- Red cells highlight areas where patrols lingered (e.g., camps, waterholes, incident sites)

---

### Example 2: Where did patrols cover the most ground?
**Goal**: See which areas patrols travelled through most, regardless of how long they stayed

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Patrol Types**: `"ecoscope_patrol"`
- **Density Calculation**: `Distance`

**Result**:
- A density map where each grid cell is colored by the total distance travelled inside it
- Highlights heavily-used routes and transit corridors rather than stopping points

---

### Example 3: Compare patrol coverage month by month
**Goal**: See how patrol effort shifted between January and February, patrol by patrol

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Patrol Types**: `"ecoscope_patrol"`
- **Group Data**:
  - Category: `Patrol Serial Number`
  - Time: `Month (example: September)`
- **Density Calculation**: `Time`

**Result**:
- The dashboard gains filter controls for patrol serial number and month
- Each combination shows its own density map, so you can compare coverage across patrols and months

---

### Example 4: Relative coverage with LTD percentiles
**Goal**: Show which areas fall in the top percentiles of patrol time density, independent of absolute effort

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Patrol Types**: `"ecoscope_patrol"`
- **Density Calculation**: `Normalised (LTD)`
- **Percentile Levels**: `50`, `90`, `100`

**Result**:
- A density map where cells are binned into your chosen percentile levels of time density
- Useful for standardized comparisons across regions or reporting periods

---

### Example 5: Fine-grained map with a custom grid
**Goal**: Produce a more detailed density map with smaller grid cells

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Patrol Types**: `"ecoscope_patrol"`
- **Density Calculation**: `Distance`
- **Advanced Configurations** (Patrol Track Density Map):
  - Heatmap Layer Opacity: `0.5`
  - Grid Cell Size: `Customize` with Custom Grid Cell Size `2500`
  - Coordinate Reference System: `EPSG:3395`

**Result**:
- A finer-resolution density map (2.5 km cells instead of auto-scaled) with a more transparent overlay so the base map shows through
- Note: Smaller cells take longer to compute and can look patchy if patrol data is sparse

## Troubleshooting

### Common Issues and Solutions

#### Workflow fails to start
**Problem**: The workflow fails immediately with a connection or authentication error

**Solutions**:
- Verify your EarthRanger data source is correctly configured in Ecoscope Desktop
- Check that your username and password are still valid — you may need to re-enter credentials for the session
- Confirm your EarthRanger server is reachable from your network (some servers require VPN access)

#### No patrols returned
**Problem**: The workflow completes but the dashboard is empty, or reports no data

**Solutions**:
- Verify patrols exist in EarthRanger during your chosen time range
- Check the patrol type spelling — it must match a patrol type configured in your EarthRanger Admin site under **Activity → Patrol Types**
- Try leaving **Patrol Types** empty to include all patrol types
- Check the **Patrol Status** advanced setting — by default only `done` patrols are included; clear it or add `active` to include in-progress patrols
- Enable **Patrols Overlap Daterange** (on by default) if your patrols start before or end after the time range

#### Patrol Types dropdown is empty
**Problem**: No options appear when configuring Patrol Types

**Solutions**:
- Select your **Data Source** first — the patrol type options are loaded from the selected EarthRanger server
- Verify patrol types are configured in your EarthRanger Admin site at `https://<your-site>.pamdas.org/admin/activity/patroltype/`
- Check your EarthRanger user account has permission to view patrols

#### Workflow runs very slowly
**Problem**: The workflow takes a long time to complete

**Solutions**:
- The first run after installation may be slower while the workflow environment "warms up" — subsequent runs are faster
- Reduce the time range to fetch fewer patrols
- Limit the analysis to specific patrol types instead of all types
- If you customized the grid cell size, try a larger value — smaller cells require more computation

#### Density map looks blank or all one color
**Problem**: The map renders but the density grid is missing, faint, or uninformative

**Solutions**:
- Check the **Heatmap Layer Opacity** advanced setting — a value near 0 makes the grid nearly invisible
- If all cells are one color, your patrol effort may be very uniform — try a smaller custom grid cell size for more detail, or switch the **Density Calculation** (e.g., from Time to Distance)
- Zoom out — the density grid only covers the area where patrol tracks exist
- If you set a **Bounding Box** filter, verify it actually contains your patrol area

#### Tracks look wrong or have long straight jumps
**Problem**: The density map shows effort in places patrols never went, or thin lines of cells crossing the map

**Solutions**:
- GPS errors can create implausible track segments. Tighten the **Trajectory Filter** advanced settings — for example, lower the Maximum Segment Length or Maximum Segment Speed to drop unrealistic jumps
- Add known bad coordinates (e.g., `(0, 0)`) to **Filter Exact Point Coordinates**
- Use the **Bounding Box** filter to exclude observations far outside your area of interest

#### Grouped views are missing some groups
**Problem**: After setting a grouper, some expected patrol types, subjects, or time periods don't appear

**Solutions**:
- Groups with no patrol tracks in the time range are skipped — verify the missing group has data in EarthRanger
- For time groupers, only periods within your **Time Range** appear
- Check the timezone setting — a patrol near midnight may fall into a different day or month than expected

## Author

Yun Wu

## License

BSD-3-Clause
