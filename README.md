# Lunar Landing Site Navigator

# NASA Space Apps Challenge 2026

## The Next Frontier

The **NASA International Space Apps Challenge** is an annual global hackathon that brings together people from different backgrounds to address challenges related to Earth and space exploration.

Organized by NASA and supported by international space agency partners, the event encourages participants to use open data, scientific knowledge, technology and creative problem-solving to develop solutions to real-world challenges.

The 2026 edition took place on **14–15 November 2026**, under the theme:

> **The Next Frontier**

The theme invited participants to explore new possibilities in science, technology and exploration, contributing ideas and tools that can help humanity advance from Earth towards the Moon, Mars and beyond.

## A global event

NASA Space Apps is designed for collaboration. Participants form teams and work intensively over the hackathon weekend to interpret a challenge, explore relevant NASA Open Data, develop a concept and build a prototype or other project deliverable.

The event welcomes different areas of expertise, including:

- Software development and engineering.
- Data science and data visualization.
- Astronomy and space science.
- Geospatial analysis.
- Design and user experience.
- Communication and storytelling.
- Research and problem-solving.

The value of this format is that complex challenges can be approached from multiple perspectives. Scientific questions can be translated into practical tools, while technical solutions can be made more accessible through good design and communication.

## Our participation

In the 2026 edition, our team, **Datanauts**, selected the challenge **Lunar Landing Site Navigator**.

We focused on the difficulties involved in comparing potential lunar landing locations, particularly in the lunar south-polar region, where terrain can strongly affect sunlight and direct-to-Earth communications.

Our project explores how lunar geospatial information can be organized and visualized to make these comparisons more accessible.

## Official resources

- [NASA Space Apps Challenge 2026](https://www.spaceappschallenge.org/2026/)
- [About NASA Space Apps](https://www.spaceappschallenge.org/about/)
- [2026 Challenge Catalogue](https://www.spaceappschallenge.org/2026/challenges/)

## A note on affiliation

This project was developed by participants in the NASA Space Apps Challenge. It is an independent team project and should not be interpreted as an official NASA product, endorsement or operational mission-planning system.

## Project Overview

The **Lunar Landing Site Navigator** is a geospatial exploration and decision-support platform focused on identifying, visualizing and comparing potential lunar landing areas, particularly around the lunar south pole.

The project brings together lunar terrain information, illumination conditions, communications visibility, mission information and user-generated observations in a common spatial framework. Its goal is to make complex lunar surface information easier to explore and compare, while providing a foundation for collaborative analysis of potential landing locations.

The platform is organized into five repositories:

- **`frontend`** — the interactive user interface and geospatial visualization.
- **`backend`** — the application logic and services that support the frontend.
- **`data`** — the datasets, precomputed values and spatial information used by the platform.
- **`api`** — the interfaces and data contracts through which the different components communicate.
- **`deliverables`** — project documentation, demonstrations, presentations and other project outputs.

Together, these repositories form a modular system in which data preparation, spatial analysis, application services and visualization are separated but connected.

---

## 1. The central concept: a common lunar grid

A central design decision is to use the **Lunar Geospatial Reference System (LGRS)** as the common spatial reference for organizing and comparing information.

Instead of treating the Moon as billions of independent latitude/longitude points, the platform works with a grid of spatial cells. Each cell represents a defined geographic area and can act as a common reference for different datasets and analysis results.

This changes how the platform organizes information:

- Terrain information can be associated with the cells it covers.
- Illumination calculations can be stored or referenced by cell.
- Communications visibility can be associated with the same cells.
- Candidate landing locations can be represented by one or more cells.
- Mission information and user annotations can be linked to those cells.
- Results from different dates or scenarios can be compared using the same spatial identifiers.

The LGRS cell identifier therefore acts as a common spatial key across the platform.

### Why use grid cells instead of individual coordinates?

Latitude and longitude are essential for locating a position on the lunar surface. However, using every coordinate sample as an independent entity would make it harder to integrate different datasets, compare scenarios and manage collaborative annotations.

A grid-based approach provides a consistent spatial unit.

For example, a terrain dataset, an illumination calculation and a communications analysis may originate from different sources and have different resolutions. By relating their results to a common grid, the platform can organize information around the same geographic areas.

This does not mean that the original datasets or their underlying coordinates are discarded. High-resolution terrain data and other source information can remain in their appropriate formats. The grid provides a common spatial framework for indexing, aggregating and comparing their results.

The practical benefits are:

1. **Consistency:** different types of information can be associated with the same spatial cells.
2. **Comparability:** the same cells can be evaluated under different dates or scenarios.
3. **Efficiency:** the application can work with summarized cell-level information instead of loading every underlying coordinate sample.
4. **Integration:** external datasets and user observations can refer to a shared spatial identifier.
5. **Scalability:** additional datasets and analysis layers can be connected without creating a separate spatial system for each one.

---

## 2. Comparing precomputed locations using LGRS

One of the main purposes of the grid is to make it possible to compare potential landing areas under consistent environmental and operational conditions.

A candidate location should not be evaluated only as a point on a map. Its suitability depends on several parameters, including:

- Terrain and surface characteristics.
- Solar illumination and shadow conditions.
- Communications visibility.
- The date and time of the analysis.
- Any operational constraints defined by the mission scenario.

Some of these properties are relatively static, while others change over time.

For example, terrain elevation does not change significantly between two nearby observation times, but the position of the Sun changes continuously. Communications visibility may also change depending on the position of the relevant relay or orbital asset.

The grid makes it possible to keep these dimensions separate while relating them to the same geographic cells.

### 2.1 Precomputing conditions for each cell

Rather than recalculating every parameter whenever a user explores the map, the platform can use precomputed analysis results.

Conceptually, each grid cell can have a collection of attributes such as:

- Terrain-related attributes.
- Illumination results for a given time or time window.
- Communications visibility for a given configuration.
- Derived indicators used to filter or rank candidate areas.
- References to the datasets and assumptions used in the calculation.

A simplified conceptual representation could look like this:

```json
{
  "cell_id": "LGRS_CELL_001",
  "terrain": {
    "elevation": "...",
    "slope": "..."
  },
  "illumination": {
    "scenario_id": "SCENARIO_A",
    "time": "TIME_A",
    "value": "..."
  },
  "communications": {
    "scenario_id": "SCENARIO_A",
    "visible": true
  }
}
```

This is an illustrative structure, not a specification of the current data files.

The important point is that the results are associated with a stable spatial cell identifier. Different analysis results can therefore be compared without having to match millions of independent coordinate samples.

### 2.2 Comparing locations under the same parameters

Suppose we want to compare three candidate landing areas.

We can evaluate their corresponding LGRS cells using the same:

- Illumination threshold.
- Communications visibility requirements.
- Terrain constraints.
- Analysis date and time.
- Operational assumptions.

This creates a controlled comparison.

For example, if all three areas are evaluated at the same time and under the same communications configuration, differences in the results are spatial differences rather than differences caused by changing the analysis conditions.

The system can then identify which cells satisfy all the selected conditions.

Conceptually:

```text
Candidate area A ──┐
Candidate area B ──┼── Same time + same parameters ──> Comparison
Candidate area C ──┘
```

This is particularly useful when comparing potential landing regions that are geographically distant from one another.

### 2.3 Comparing the same locations at different times

The same approach can be used to study how conditions evolve.

Because the LGRS cell identifier remains the same, we can compare the results for a cell at different times:

```text
LGRS_CELL_001

Time A:
  Illumination: result A
  Communications: result A

Time B:
  Illumination: result B
  Communications: result B

Time C:
  Illumination: result C
  Communications: result C
```

The terrain reference remains associated with the same cell, while time-dependent attributes are evaluated for each scenario or time window.

This allows the platform to answer questions such as:

- Which areas are illuminated at a specific time?
- Which areas have communications visibility during a particular operational window?
- Which locations remain suitable across several consecutive time windows?
- Which areas become illuminated or shadowed as time passes?
- How does the available landing area change between two dates?
- Which candidate locations provide the most consistent combination of illumination and communications?

An important distinction is that **comparing different times requires time-specific analysis results**. A cell identifier alone does not tell us whether a location is illuminated or has communications visibility. Those values must be calculated or retrieved for the relevant time and scenario.

### 2.4 Keeping comparisons scientifically consistent

For a meaningful comparison, the platform should distinguish between two types of parameters.

**Fixed comparison parameters**

These are held constant when comparing locations or dates:

- Grid definition and spatial resolution.
- Terrain constraints.
- Communications configuration, including the selected assets and visibility criteria.
- Illumination criteria.
- Any operational thresholds.

**Changing parameters**

These are deliberately varied:

- Geographic location.
- Date or time.
- Selected mission scenario.
- Communications configuration, when testing an alternative scenario.

This distinction helps users understand whether a difference in the result is caused by geography, time or a change in the assumptions.

For example, when comparing the same cell at two different times, the communications configuration and thresholds should remain fixed if the intention is to isolate the effect of time.

When comparing two communications configurations, the date and other conditions should remain fixed if the intention is to isolate the effect of the configuration.

---

## 3. How the grid system is represented in the interface

The grid is not only a data-management mechanism. It is also the spatial structure that makes the analysis understandable to users.

The frontend can display the lunar surface as a continuous terrain model while overlaying the LGRS cells on top of it.

Each cell can be associated with information and visual states.

For example:

- Cells meeting the selected illumination criteria can be highlighted.
- Cells with communications visibility can be displayed using a separate visual layer.
- Cells satisfying both conditions can be identified as candidate areas.
- Cells that fail one or more constraints can be displayed differently.
- Cells containing user annotations can be marked and opened for inspection.

The visual representation should make it possible to understand both the geographic distribution of the results and the information associated with an individual cell.

### 3.1 Grid-based visualization

The grid can be displayed as an overlay on the lunar terrain.

The user can explore the surface in 2D or 3D and inspect the cells covering a particular region.

Selecting a cell can expose its associated information, such as:

- LGRS identifier.
- Geographic location or coordinate reference.
- Terrain attributes.
- Illumination conditions.
- Communications visibility.
- Related mission information.
- User annotations.

The grid therefore connects the visual representation of the Moon with the underlying analysis and data model.

### 3.2 Visualizing different conditions

Different analysis layers can be displayed independently or combined.

For example:

| Layer | What it represents |
|---|---|
| Terrain | Surface morphology and elevation-related information |
| Illumination | Solar illumination or shadow conditions |
| Communications | Visibility according to the selected communications configuration |
| Candidate areas | Cells satisfying the selected operational criteria |
| Missions | Locations or regions associated with lunar missions |
| User annotations | Observations and classifications contributed by users |

These layers should remain conceptually separate. A cell can be suitable according to one parameter and unsuitable according to another.

Combining layers allows the user to identify areas where multiple requirements overlap.

### 3.3 Visualizing time comparisons

Time-dependent analysis can be represented through several complementary interface patterns:

- **Time slider:** move through time and observe how illumination or communications conditions change across the grid.
- **Side-by-side comparison:** display the same region at two different times.
- **Difference view:** highlight cells whose status changes between two selected times.
- **Scenario comparison:** compare different operational configurations while keeping the geographic reference consistent.
- **Cell inspection:** select a cell and inspect its values across multiple time windows.

For example, a difference view could distinguish:

- Cells suitable at both times.
- Cells suitable only at the first time.
- Cells suitable only at the second time.
- Cells unsuitable at both times.

This is particularly useful for identifying areas with persistent illumination or communications conditions, rather than areas that satisfy the requirements only at one isolated moment.

The exact classification and visual encoding depend on the analysis outputs and thresholds implemented by the application.

---

## 4. Repository architecture

The project is divided into five repositories to separate responsibilities and make development more manageable.

### 4.1 `frontend`

The frontend is the user-facing part of the platform.

It provides the interactive environment through which users explore lunar geography, inspect candidate landing locations and interact with the available data.

Its main responsibilities include:

- Rendering the lunar surface.
- Providing 2D and 3D geospatial visualization.
- Displaying the LGRS grid.
- Showing terrain and analysis layers.
- Allowing users to inspect individual cells and candidate locations.
- Supporting comparisons between locations and time-dependent scenarios.
- Displaying mission information.
- Providing the interface for user annotations and observations.

The frontend should focus on visualization and user interaction. Heavy geospatial calculations and data-management operations should be handled by the appropriate backend services or precomputed datasets.

**Main technologies:** React, TypeScript and Three.js.

### 4.2 `backend`

The backend provides the application-side logic that supports the frontend.

It acts as the intermediary between the user interface, the underlying data and the services exposed through the API.

Its responsibilities may include:

- Retrieving and organizing data.
- Providing cell-level information.
- Handling application logic.
- Managing user-generated annotations.
- Connecting annotations to their corresponding spatial cells.
- Applying validation and consistency rules.
- Preparing information for visualization and comparison.
- Coordinating access to the different data sources.

The backend is particularly important for ensuring that the frontend does not need to understand the internal structure of every dataset.

For example, the frontend should be able to request the information associated with an LGRS cell without needing to know how that information was originally calculated or stored.

### 4.3 `data`

The data repository contains the datasets used by the platform.

It provides the spatial and contextual information needed to explore the lunar surface and evaluate potential landing areas.

Depending on the dataset, this may include:

- Lunar terrain and topographic information.
- Data derived from NASA LOLA and LROC products.
- LGRS grid information.
- Precomputed site and mission information.
- Illumination-related results.
- Communications-related results.
- Horizon or terrain visibility information.
- Other derived geospatial attributes.

The repository should distinguish between:

1. **Source data:** information obtained from external datasets.
2. **Processed data:** information transformed into a format suitable for the application.
3. **Precomputed analysis:** results calculated in advance for specific locations, times or scenarios.
4. **Reference data:** mission catalogs, candidate locations and other contextual information.

Where possible, each dataset should document its source, units, coordinate reference, resolution, processing assumptions and update date.

This is important because two datasets can describe the same geographic region while using different resolutions, units or conventions.

The LGRS provides a common spatial reference for connecting them, but it does not automatically make their underlying measurements equivalent.

### 4.4 `api`

The API defines how the different parts of the system communicate.

It provides the interface between the frontend, backend and data services.

Its purpose is to make the system modular: the frontend should not need to access internal database structures or understand the physical organization of the data repository.

The API can expose operations such as:

- Retrieve available missions.
- Retrieve candidate landing locations.
- Retrieve information associated with an LGRS cell.
- Retrieve terrain and analysis attributes.
- Retrieve illumination and communications results for a selected time or scenario.
- Compare cell-level results between two times.
- Submit and retrieve user annotations.

The API should also define consistent data structures, field names, identifiers and validation rules.

For example, using the same `cell_id` across the API, backend and data repository allows the different components to refer to the same spatial area without introducing separate location identifiers.

### 4.5 `deliverables`

The deliverables repository contains the documentation and outputs associated with the project.

It can include:

- Technical documentation.
- Architecture diagrams.
- Project presentations.
- Demonstration material.
- Screenshots and visual explanations.
- Hackathon deliverables.
- Supporting reports and other project outputs.

Its purpose is to preserve the project's technical and communication outputs independently from the application code.

---

## 5. How the repositories work together

The five repositories represent different responsibilities in one workflow.

A simplified interaction is:

```text
                    DATA
                     |
          Source and precomputed data
                     |
                     v
                  BACKEND
                     |
          Application logic and data access
                     |
                     v
                    API
                     |
          Structured data and operations
                     |
                     v
                  FRONTEND
                     |
          2D / 3D visualization and interaction
                     |
                     v
              USER EXPLORATION
                     |
          Inspection and annotations
```

The `deliverables` repository documents the system and its outputs across the different stages.

A typical user workflow could be:

1. The user opens the lunar surface visualization.
2. The frontend requests the relevant spatial information.
3. The backend retrieves the necessary data through the available data services.
4. The API provides the information in a structured format.
5. The frontend renders the terrain, grid and selected analysis layers.
6. The user selects an LGRS cell.
7. The application displays the information associated with that cell.
8. The user compares the cell with other locations or time windows.
9. The user can contribute an observation or annotation associated with the relevant spatial area.

This separation makes it possible to improve one component without redesigning the entire platform.

---

## 6. User annotations and the future role of Memgraph

An important future development is the integration of user-generated annotations with the scientific and geospatial information already available in the platform.

Users may identify and describe:

- Potential landing hazards.
- Rocks or boulder concentrations.
- Terrain irregularities.
- Shadow boundaries.
- Areas with interesting illumination conditions.
- Potential landing zones.
- Observations that require further investigation.

These annotations are valuable because they add contextual information that may not be available in the original datasets.

However, simply storing annotations as independent records is not enough. Their real value comes from connecting them to the surrounding geographic and scientific context.

This is where a graph database such as **Memgraph** could become useful.

Memgraph was considered as a possible component of the architecture, but it was not implemented during the hackathon because of time constraints. The following describes a proposed future architecture, not functionality currently implemented.

### 6.1 Why a graph database?

The platform contains different kinds of entities that are related to one another.

For example:

- A user creates an annotation.
- The annotation refers to an LGRS cell.
- The cell belongs to a particular geographic region.
- The cell has terrain attributes.
- The cell has illumination results for different times.
- The cell has communications visibility results.
- The cell may be associated with a candidate landing site.
- The site may be associated with one or more missions.

These relationships are naturally represented as a graph.

A graph database allows the platform to represent not only the individual records, but also the connections between them.

Instead of repeatedly joining large collections of independent records, the system can traverse relationships between related entities.

### 6.2 The grid cell as the central graph entity

The most important architectural decision would be to use the LGRS cell as the common spatial node in the graph.

The graph would not need to contain every latitude/longitude coordinate or every underlying terrain sample.

Instead, each LGRS cell would act as a reference point connecting information from different sources.

A simplified graph model could contain the following node types:

| Node | Purpose |
|---|---|
| `GridCell` | Represents an LGRS spatial cell |
| `LandingSite` | Represents a candidate or known landing location |
| `Mission` | Represents a lunar mission |
| `Annotation` | Represents a user-generated observation |
| `User` | Represents the contributor |
| `Dataset` | Represents a source or processed dataset |
| `AnalysisResult` | Represents a result for a particular cell, time and scenario |
| `Scenario` | Represents the conditions and parameters used for an analysis |

Relationships could include:

| Relationship | Meaning |
|---|---|
| `LOCATED_IN` | A landing site or annotation is associated with a grid cell |
| `ANNOTATED` | A user created an annotation |
| `HAS_ANNOTATION` | A grid cell has one or more annotations |
| `ASSOCIATED_WITH` | A site is associated with a mission |
| `HAS_RESULT` | A cell has an analysis result |
| `GENERATED_FROM` | An analysis result was generated from a dataset |
| `EVALUATED_UNDER` | A result belongs to a scenario |
| `NEIGHBOURS` | Two cells are spatially adjacent |

The exact model should be adapted to the actual LGRS cell hierarchy and the relationships supported by the datasets.

### 6.3 Integrating annotations with scientific data

Imagine a user identifies a possible hazard in a particular area.

The annotation could contain:

- An annotation identifier.
- The user who created it.
- A description.
- A classification, such as `boulder`, `crater`, `slope_hazard` or `shadow_boundary`.
- A timestamp.
- A confidence level or review status.
- A reference to the relevant LGRS cell.
- Optionally, a more precise coordinate or geometry.

The annotation would be connected to the corresponding `GridCell` node.

That cell could already be connected to terrain information, illumination results, communications visibility and mission data.

This creates a connection between citizen-science observations and existing scientific information.

For example:

```text
User
 |
 | ANNOTATED
 v
Annotation
 |
 | LOCATED_IN
 v
GridCell
 |
 +---- HAS_RESULT --------> Illumination result
 |
 +---- HAS_RESULT --------> Communications result
 |
 +---- ASSOCIATED_WITH --> Landing site
 |
 +---- GENERATED_FROM ---> Dataset
 |
 +---- NEIGHBOURS --------> Adjacent GridCell
```

A user could therefore select a cell and see not only its precomputed scientific attributes, but also the observations contributed by other users.

Conversely, the platform could use the existing scientific data to provide context for an annotation.

For example, an annotation describing a shadow boundary could be compared with the illumination results calculated for the same cell and time.

An annotation describing a potential landing hazard could be inspected alongside terrain slope or other available terrain attributes.

These connections make the annotations more useful than isolated map markers.

### 6.4 Why the graph should use LGRS cells instead of billions of coordinates

Creating a graph node for every latitude/longitude sample would be unnecessary and potentially expensive.

The underlying geospatial datasets may contain very large numbers of measurements. Their original spatial resolution and structure should be preserved in the appropriate data storage systems.

Memgraph would instead represent the relationships between meaningful spatial entities.

The LGRS cell provides a practical aggregation and indexing level.

For example:

```text
Underlying geospatial data
  ├── Terrain samples
  ├── Elevation measurements
  ├── Illumination calculations
  └── Communications calculations
             |
             v
       LGRS Grid Cell
             |
             v
       Memgraph node
             |
             +── User annotations
             +── Landing sites
             +── Missions
             +── Analysis results
             +── Related datasets
```

The graph stores the relationships and references. It does not need to duplicate every original terrain measurement.

Detailed raster data, elevation models and large arrays can remain in specialized geospatial storage or files. The graph can store the cell identifier, relevant attributes, dataset references and relationships.

This separation avoids turning the graph database into a replacement for the entire geospatial data infrastructure.

### 6.5 Example: finding annotated cells that satisfy operational conditions

Consider a future query:

> Find cells that satisfy the illumination and communications requirements for a selected time window, contain no unresolved high-priority hazards, and belong to a candidate landing region.

This query combines several types of information:

1. The spatial identity of the cell.
2. Precomputed illumination results.
3. Precomputed communications results.
4. User annotations.
5. Landing-site associations.
6. The selected time and operational scenario.

A graph can connect these entities and make the relationships explicit.

A simplified conceptual query could look like this:

```cypher
MATCH (cell:GridCell)-[:HAS_RESULT]->(illum:AnalysisResult)
MATCH (cell)-[:HAS_RESULT]->(comm:AnalysisResult)
MATCH (cell)-[:HAS_ANNOTATION]->(annotation:Annotation)
MATCH (cell)-[:LOCATED_IN]-(site:LandingSite)

WHERE illum.type = "illumination"
  AND comm.type = "communications"
  AND illum.time = $selectedTime
  AND comm.time = $selectedTime
  AND illum.passes = true
  AND comm.passes = true
  AND annotation.status = "unresolved"
  AND annotation.priority = "high"

RETURN cell, site, annotation
```

This is an illustrative query showing the intended relationships. It is not executable against the current project without implementing the corresponding graph schema, properties and relationships.

A production query would also need to define how multiple annotations are aggregated and how a cell with no annotations is handled. In particular, the absence of an annotation must not automatically be interpreted as proof that a cell is hazard-free.

### 6.6 Combining graph relationships with geospatial calculations

Memgraph would complement, rather than replace, the existing geospatial analysis.

A possible division of responsibilities would be:

| Component | Responsibility |
|---|---|
| Geospatial processing | Terrain processing, coordinate operations and scientific calculations |
| Precomputed data | Store or expose cell-level analysis results |
| LGRS | Provide the common spatial reference |
| Memgraph | Connect cells, missions, datasets, scenarios and annotations |
| Backend/API | Retrieve and combine the information |
| Frontend | Visualize the grid, analysis results and annotations |

This architecture keeps computationally intensive geospatial operations separate from relationship management.

It also makes it possible to add new types of information without redesigning the entire spatial data model.

For example, a future dataset could introduce a new terrain-risk indicator. Once its results are associated with LGRS cells, those results could be connected to the graph and made available alongside existing annotations and analysis results.

---

## 7. Important data and analysis principles

To make comparisons reliable, the platform should follow a few principles.

### 7.1 Keep spatial identifiers consistent

The same LGRS cell should have the same identifier throughout the system.

The frontend, backend, API, datasets and future graph database should all use the same identifier.

This is essential for joining information from different sources.

### 7.2 Keep time and scenario information explicit

Time-dependent results should not be stored as if they were permanent properties of a location.

Illumination and communications results should identify the relevant time or time window and the scenario under which they were calculated.

A result should be traceable to its calculation assumptions.

### 7.3 Preserve source information

Precomputed values should retain references to their source datasets and processing methodology.

This allows users and developers to understand where a result came from and whether two results are genuinely comparable.

### 7.4 Distinguish observations from calculated results

A user annotation is not equivalent to a scientific measurement or a verified hazard classification.

The system should preserve the distinction between:

- Original scientific data.
- Derived analysis results.
- User observations.
- Reviewed or validated annotations.

This is especially important if user contributions are later used in decision-support workflows.

### 7.5 Do not interpret missing data as a positive result

A cell without an illumination result is not necessarily illuminated.

A cell without a communications result is not necessarily disconnected.

A cell without a user annotation is not necessarily hazard-free.

The system should represent missing, unknown, failed and not-applicable values explicitly.

---

## 8. Current implementation and future direction

The hackathon implementation established the foundations of the Lunar Landing Site Navigator by organizing the project into separate repositories and bringing together lunar geospatial information, candidate locations and interactive visualization.

The LGRS-based approach provides a common spatial framework for connecting different kinds of information and comparing locations under consistent conditions.

However, some architectural ideas remain future work.

In particular:

- A complete time-dependent comparison workflow depends on the availability of precomputed results for the relevant times and scenarios.
- The integration of all datasets into a unified cell-level data model can be developed further.
- A complete annotation workflow requires persistent storage, validation and review mechanisms.
- Memgraph was considered as a way to connect annotations with cells, missions, datasets and analysis results, but was not implemented during the hackathon.
- The graph schema, ingestion process and query layer would need to be designed and implemented before this functionality could be used.

The proposed Memgraph integration is therefore a future architectural direction, not a description of an existing feature.

---

## 9. Overall vision

The Lunar Landing Site Navigator aims to provide a common environment for exploring lunar surface conditions and evaluating potential landing locations.

Its architecture is based on three complementary ideas:

**1. A common spatial framework**

LGRS cells provide a consistent geographic reference for connecting terrain, illumination, communications, missions and user observations.

**2. Comparable precomputed analysis**

By associating results with the same cells and recording their time and scenario parameters, the platform can compare different locations under the same conditions and study how the same location changes over time.

**3. Connected scientific and community knowledge**

A future graph-based layer using Memgraph could connect user annotations with the underlying geospatial information, making it possible to explore the relationships between observations, terrain, landing sites, missions and analysis results.

The result is intended to be more than a lunar map. It is a spatial information platform in which different datasets and observations can be explored through a shared grid, compared across time and progressively connected into a broader knowledge base.

---

## 10. Repository map

| Repository | Main responsibility |
|---|---|
| `frontend` | Interactive application, 2D/3D visualization and user interaction |
| `backend` | Application logic, data access and annotation handling |
| `data` | Source datasets, processed geospatial data and precomputed results |
| `api` | Interfaces, endpoints and shared data contracts |
| `deliverables` | Documentation, presentations and project outputs |

For repository-specific installation instructions, dependencies, environment variables and execution commands, refer to the README inside each repository.

## 11. Terminology

| Term | Meaning |
|---|---|
| **LGRS** | Lunar Geospatial Reference System, used as the common spatial grid reference |
| **Grid cell** | A spatial unit in the grid, identified by a cell identifier |
| **Precomputed result** | An analysis value calculated in advance for a particular location, time or scenario |
| **Illumination** | The solar lighting conditions at a location and time |
| **Communications visibility** | Whether a location satisfies the visibility criteria for the selected communications configuration |
| **Scenario** | A defined set of assumptions and parameters used for an analysis |
| **User annotation** | An observation or classification contributed by a user |
| **Graph database** | A database designed to represent entities and the relationships between them |
| **Memgraph** | A graph database considered for connecting spatial cells, annotations and other project information |

---

*This document describes the overall project architecture and its intended direction. Repository-specific implementation details should be documented in the corresponding repository README files.*
