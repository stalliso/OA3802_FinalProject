# Near Real-Time AIS Tracking

## Project Description
This project delivers an end-to-end streaming analytics pipeline for AIS data, combining real-time ingestion, parallel analytics, and HPC operations. Our outcomes include data-driven insights into maritime traffic flow and anomalies near a specific port, API streaming, Bash automation, virtual environments, and visualizations.

## HPC Work
On the NPS Hamming HPC, a py script was created that connects to wss://stream.aisstream.io/v0/stream, which is a live stream of AIS data. For five minutes, the messages are screened while looking for PositionReport or ShipStaticData. The timestamps are also recorded to be able to see routes of ships as more data is collected. The PositionReport is saved as ais_data and the ShipStaticData as ship_data json files. The json files were saved in two output folders named data_output and ship_output.

A second .py script was created to consolidate the json files for both ship_data and ais_data into parquet files. The paths to the data_output and ship_output folders were defined. Data frames were created for ais and ship based on the folders, where you focus on only the json files. Once all the json files have been consolidated in the dfs, they are written as parquet files. 

In order to capture the data at regular intervals, we created a batch script and used the `crontab` bash command on HPC to schedule it to pull five minutes of AIS tracks and ship static data every hour, on the hour. After getting familiar with the scheduling function, we also created a batch script to compile the hourly AIS track and ship data into a single parquet file to build our future visualizations.

## Required Material:
-   OA3802_FinalProject.ipynb: notebook used to execute the project.
-   Ais_data.parquet: a parquet file for the AIS data collected for the project.
-   Ship_data.parquet: a parquet file used to hold all the ship data.
-   Requirements.txt: specifies all the settings and versions of all the programs needed to execute the visualisations. 
-   Visualization_fleets.xlsx (available upon request): excel used to hold ship data for vessels that have entered and departed from Ukrainian ports illegally occupied by Russia, such as name, owner, country of origin, and activity.

## Instructions
There are several commented cells or markdown cells in this notebook. The only cells that will be addressed are those needed to replicate the project and are in order as they appear in the OA3802_FinalProject.ipynb notebook.
1.	Start by running the import packages code cell.
2.	The next cell is ran to pull the live ship tracking messages from land-based antennas to specifically pull the PositionReport data. The data is exported to .json in the present working directory (pwc) and then stored in data frames.
3.  The cell after shows an example of how the message reports are pulled.
4.  From there, you produce a visualization of the initial data using bokeh (for running in VS Code, requires virtual environment configured with requirements.txt).
5.	The next cell retrieves the most current .parquet files from HPC using username (first.last) and user's HPC password. These files are stored at home/stuart.allison/project. Permissions were altered so that all users in the HPC group should be able to access, but if there are issues, please reach out. The parquet files are then read into pandas df's from the pwd and containt the AIS data as well as the ship data.
6.	Once the data is loaded as df's, a heatmap is created using the data from 03 November to Present.
7.	The next two cells read in the visualization_fleets.xlsx to store the ships that have entered and departed from Ukrainian ports currently illegally occupied by Russia. You produce the first and last five rows of the excel showing the relevant information.
8.	The next cell produces a choropleth of the country of origin for the vessels that have been tagged as entering the Ukrainian ports mentioned above, with scaled values of number of ships determining color.
9.  After looking at the map, the next five cells are used to assess the difference between the countries with robust flags compared to those that operate under flags of convenience.
10.	The illegal activity data provided mostly IMO ship numbers, as they are more difficult to change to conceal identity, whereas the the PositionReport provided only MMSI. We do some pandas filtering to cross reference the illegal activity IMO's with MMSI's, then plot our track data for ONLY these MMSI's.
11.	A Folium map is created to show the density of vessels engages in illegal activities over time by grouping them into buckets and visualize their locations through time.

## Lessons Learned
Originally, we used an API to pull near-live AIS data to visualize locations. However, the bokeh package has version compatibility issues with the most recent python version, so we created a new virtual environment to create this presentation. Through this, we learned it was easy to transfer upload the exact requirements for the venv.

We discovered that GitLab has an HTTPS transfer threshold of around 400MB, so we had to install Git Large File Storage (LFS) with an SSH key to successfully commit and push updates to these .parquet files.

## Future Work
Continue developing these visualizations, adding filtering capability by commodity type, fleet owner, or flag. Additionally, compiling all of the visualizations into a singular dashboard would be beneficial and allow for a better UI. Finally, figuring out how to more effectively incorporate the live api data into an actually live feature would be valuable.

Additionally, using paid membership or other service, track port of departure, destination port, etc to more robustly track potentially illegal activity.








