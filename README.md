# CS 499 Capstone ePortfolio — Grazioso Salvare Dashboard

**Author:** Amanda Willbanks
**Program:** B.S. Computer Science, Southern New Hampshire University

---

## Origin and Purpose

This repository is my CS 499 Capstone ePortfolio. It contains an enhanced version of the Grazioso Salvare Dashboard, a project I originally built in **CS-340: Client/Server Development** (December 2025). The original project is available at [amandawillbanks/CS340](https://github.com/amandawillbanks/CS340).

For the capstone, I selected this artifact because it is a complete, full-stack application that touches database integration, interactive UI design, data visualization, and backend logic — all within a single codebase. Rather than submitting surface-level work across unrelated projects, I chose to demonstrate depth by enhancing one artifact across three categories, showing that I can identify structural weaknesses through a code review, plan targeted improvements, and execute them with attention to security, performance, maintainability, and user value.

## Enhancements

The original dashboard was functional but had significant engineering gaps: hardcoded credentials, rescue filter logic embedded inside UI callbacks, fragile positional index lookups, no ranking of results, and a full 10,000-record startup load. Across three capstone milestones, I systematically addressed each one:

| Milestone | Category | Key Enhancements |
|---|---|---|
| **Two** | Software Design & Engineering | Credentials moved to environment variables; rescue criteria extracted to a configuration dictionary; magic index lookups replaced with named column references |
| **Three** | Algorithms & Data Structures | Weighted suitability scoring algorithm (max 10 pts); inverted breed index for O(1) lookup; query result caching; context-aware chart selection per filter type |
| **Four** | Databases | Query construction moved into the CRUD module (`get_rescue_candidates()`); input validation with typed exceptions; `read_sample()` for lightweight startup; documented compound index strategy |

Each enhancement is documented in a milestone narrative that explains the reasoning, course outcome alignment, and reflection.

---

## Project Overview

This project implements an interactive web-based dashboard for Grazioso Salvare, a fictional animal rescue organization. The dashboard allows users to explore and analyze animal shelter data stored in a MongoDB database. It uses the Dash framework to present dynamic tables, visualizations, and geolocation mapping while following the MVC (Model-View-Controller) design pattern.
The dashboard enables Grazioso Salvare to identify animals that are suitable for specialized rescue training scenarios such as Water Rescue, Mountain/Wilderness Rescue, and Disaster/Individual Tracking.

Technologies Used:

Python 3
Dash / JupyterDash
MongoDB
Pandas
Plotly Express
Dash Leaflet
HTML/CSS (via Dash components)

Architecture (MVC Pattern)
Model
MongoDB database accessed through a custom CRUD module (CRUD_Python_Module.py)
The AnimalShelter class abstracts all database interactions

View
Dash components (DataTable, graphs, map, radio buttons)
Grazioso Salvare logo and structured page layout

Controller
Dash callback functions manage user interactions
Callbacks update the data table, charts, and map dynamically based on user input

Dashboard Features
1. Interactive Data Table

Displays animal shelter records retrieved from MongoDB

Supports:
Sorting
Filtering
Pagination
Single-row selection
Automatically updates when a rescue filter is selected

2. Rescue Type Filtering
Users can filter animals based on rescue suitability:
All Animals
Water Rescue
Mountain/Wilderness Rescue
Disaster/Individual Tracking
Each filter triggers a MongoDB query using criteria defined in the project requirements.

3. Data Visualization
A pie chart dynamically displays the most common breeds in the current dataset view
The chart updates automatically based on applied filters

4. Geolocation Map
Displays the geographic location of the selected animal
Uses Dash Leaflet to plot latitude and longitude data
Includes interactive markers with tooltips and popups

5. Branding
Displays the Grazioso Salvare logo at the top of the dashboard
The logo is loaded and embedded using Base64 encoding for reliability in Jupyter environments

Running the Application
Requirements

Ensure the following are installed:

Python 3
MongoDB (with the Austin Animal Center dataset loaded)
Required Python packages:

dash
jupyter-dash
pandas
plotly
dash-leaflet

How to Run

Open ProjectTwoDashboard.ipynb in Jupyter or Codio.
Ensure CRUD_Python_Module.py and the logo image file are located in the same working directory.
Run the notebook cell containing the application code.
The dashboard is launched with the following command:
app.run_server(mode="inline", debug=True, port=8052)

Why a Port Is Explicitly Specified

A specific port (8052) is used to avoid conflicts with previously running Dash servers in Jupyter-based environments.
JupyterDash can sometimes attempt to reuse or terminate an existing server thread, which may cause runtime errors. Explicitly setting a port ensures the application launches reliably and avoids thread cleanup issues.

Notes and Assumptions

Latitude and longitude fields are detected automatically when possible.
If named geolocation fields are unavailable, the application safely falls back to predefined column indices.
All database credentials are hardcoded only for this academic assignment and should not be used in production environments.

Conclusion

This dashboard provides Grazioso Salvare with a powerful, interactive tool for identifying animals suitable for specialized rescue training. It demonstrates effective use of client/server development concepts, database integration, data visualization, and user-centered design using the Dash framework.
