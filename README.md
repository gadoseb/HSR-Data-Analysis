# Overview
This repository contains four Jupyter Notebooks for Hydrogen Storage Reactors (HSRs) data analysis. Here below there is a detailed explanation of the files used and code documentation for each one of the scripts. The main difference between them is the data files which were processed and the different requirements in the data handling. More detailed definition of the custom functions and methods can be found directly in the individual notebooks.

# ModularRig_DataVisualisation_V2.ipynb Documentation

## Data Files

| Type | Sample Name | Name Structure | Information | Code ID |
| --- | --- | --- | --- | --- |
| .csv | CPE78683rig3-D47 | ‘reactor code and rig No.’-’cycle No.’ | Temperature file | Group 1 |
| .csv | 20240326-133256-reactor_CPE78683-rig_3-D47 | YYYYMMDD-hhmmss-reactor_’reactor code’-’rig No.’-’cycle No.’ | Beta3 rig file(s) | Group 2 |
| .csv | D47-2024-03-26T13h33-10s | ‘cycle No.’-YYYY-MM-DDThhhmm-10s | Modular rig file(s) | Group 3 |

## Data Structure

The **bold-data** are the one captured by the code and needed for analysis.

| Group No. | Size | Type | Information |  |
| --- | --- | --- | --- | --- |
| 1 | 3 columns | 1 date-time
2 floats | Timestamp
Rig 2 IN Last (C)	
Rig 2 OUT Last (C) | Temperature |
| 2 | 19 columns (starting at row 5) | All floats | Timestamp	
Time since start (s)	
Flow (L/min)	
Pressure (Bar)	
RTD1(c)	
RTD2(c)	
Volume(L)	
M1AI1	
M2AI0	
M2AI1	
M2AI2	
M2AI3	
M2AI4	
M2AI7	
M3AI0	
M3AI1	
M3AI2	
M3AI3	
M3AI4 | Crucial for strain data |
| 3 | 40 columns | 1 date-time
39 floats | runner_timestamp (date-time)	
runner_timestamp	
message_timestamp	
NTC1	
NTC2	
NTC3	
NTC4	
NTC5	
NTC6	
NTC7	
NTC8	
NTC9	
NTC10	
NTC11	
NTC12	
NTC13	
NTC14	
NTC15	
NTC16	
4-20mA Sensor 1 (pressure)	
4-20mA Sensor 2 (not implemented yet)	
PID temperature	
Thermocouple limit temp	
MFC flow rate	
Strain 1	
Strain 2	
Strain 3	
Strain 4	
Strain 5	
Strain 6	
Strain 7	
Strain 8	
Strain 9	
Strain 10	
Strain 11	
Strain 12	
Solenoid States	
delta_t	
t (time since start)	
volume | NTC* will be temperature data (not implemented yet 042024). Solenoid States: 0=idle, 5=abs, 58=des. |

## Notebook Information
This Python script facilitates the analysis of data collected from a modular rig setup. It provides functionalities to process raw data from modular rig files, strain files, and temperature files, merge them into a single DataFrame, visualize the data through scatter plots, and export processed data subsets along with corresponding plots for further analysis and reporting.

**Code Logic**

- The script processes raw data from modular rig files, strain files, and temperature files, merges them into a single DataFrame, and generates subsets based on unique state numbers for further analysis.
- It provides visualization capabilities through scatter plots of various parameters over time.
- Export functionality allows users to save processed data subsets to CSV files and corresponding plots for archival and sharing purposes.

**Configuration**

- Adjust the folder paths, filenames, and optional parameters in the functions to suit specific analysis requirements.
- Ensure that the input data files are correctly formatted and located in the specified folders for processing.

### **Custom Functions:**

**1. `process_modular_files(foldersearchpath)`**

- **Purpose:** Process the modular rig files to extract relevant data, including timestamps, pressure, flow rate, solenoid states, and volume. The function performs data cleaning by renaming columns, setting timestamps as the index, sorting chronologically, and replacing unplugged sensor signals with NaN values.
- **Parameters:**
    - **`foldersearchpath`** (str): The path of the folder containing the modular rig files.
- **Returns:**
    - **`df_modular`** (DataFrame): Processed data from the modular rig files.
    - **`state_numbers`** (list): List of unique state numbers encountered in the files.

**2. `process_strain_files(foldersearchpath, state_numbers)`**

- **Purpose:** Process the strain files in the specified folder, extracting strain data from each file based on the provided state numbers. The function skips the first 4 rows, renames columns, sets timestamps as the index, and converts timestamps to match the format of the modular rig data.
- **Parameters:**
    - **`foldersearchpath`** (str): Path to the folder containing the strain files.
    - **`state_numbers`** (list): List of unique state numbers encountered in the modular files.
- **Returns:**
    - **`df_strain`** (DataFrame): Processed strain data with state number as an additional column.

**3. `process_temperature_files(foldersearchpath, state_numbers)`**

- **Purpose:**  Process the temperature files in the specified folder, extracting temperature data based on the provided state numbers. The function renames columns, sets timestamps as the index, and converts timestamps to match the format of the modular rig data.
- **Parameters:**
    - **`foldersearchpath`** (str): Path to the folder containing the temperature files.
    - **`state_numbers`** (list): List of unique state numbers.
- **Returns:**
    - **`df_temperature`** (DataFrame): Processed temperature data.

**4. `merge_dataframes(df_modular, df_strain, df_temperature)`**

- **Purpose:** Merge the dataframes based on the timestamps in **`df_modular`**, combining data from modular rig files, strain files, and temperature files. The function performs a timestamp-based merge, replacing unplugged sensor signals with NaN values, and adjusts temperature data for daylight saving time.
- **Parameters:**
    - **`df_modular`** (DataFrame): DataFrame containing modular rig data.
    - **`df_strain`** (DataFrame): DataFrame containing strain data.
    - **`df_temperature`** (DataFrame): DataFrame containing temperature data.
- **Returns:**
    - **`df_full`** (DataFrame): Merged DataFrame.

**5. `flow_rate_chart(df, state_number, plot_filepath=None)`**

- **Purpose:** Generate a scatter plot of flow rate over time. The function visualises the flow rate data with colors indicating absorption or desorption states.
- **Parameters:**
    - **`df`** (DataFrame): DataFrame containing flow rate data.
    - **`state_number`** (str): State number for the plot.
    - **`plot_filepath`** (str, optional): Path to save the plot. If None, the plot will not be saved.
- **Returns:**
    - None

**6. `flow_rate_temp_chart(df, name, temp_columns, plot_filepath=None)`**

- **Purpose:** Generate a scatter plot of flow rate and temperature over time. The function visualises the flow rate and temperature data with separate axes, allowing comparison between the two variables.
- **Parameters:**
    - **`df`** (DataFrame): DataFrame containing flow rate and temperature data.
    - **`name`** (str): Name for the plot.
    - **`temp_columns`** (list): List of column names containing temperature data.
    - **`plot_filepath`** (str, optional): Path to save the plot. If None, the plot will not be saved.
- **Returns:**
    - None

**7. `volume_chart(df, name, plot_filepath=None)`**

- **Purpose:** Generate a scatter plot of volume over time. The function visualises the volume data with colors indicating absorption or desorption states.
- **Parameters:**
    - **`df`** (DataFrame): DataFrame containing volume data.
    - **`name`** (str): Name for the plot.
    - **`plot_filepath`** (str, optional): Path to save the plot. If None, the plot will not be saved.
- **Returns:**
    - None

**8. `pressure_chart(df, name, plot_filepath=None)`**

- **Purpose:** Generate a scatter plot of pressure over time. The function visualises the pressure data with colors indicating absorption or desorption states.
- **Parameters:**
    - **`df`** (DataFrame): DataFrame containing pressure data.
    - **`name`** (str): Name for the plot.
    - **`plot_filepath`** (str, optional): Path to save the plot. If None, the plot will not be saved.
- **Returns:**
    - None

**9. `strain_pressure_chart(df, name, plot_filepath=None)`**

- **Purpose:** Generate a scatter plot of strain and pressure over time. The function visualizes the strain and pressure data with separate axes, allowing comparison between the two variables.
- **Parameters:**
    - **`df`** (DataFrame): DataFrame containing strain and pressure data.
    - **`name`** (str): Name for the plot.
    - **`plot_filepath`** (str, optional): Path to save the plot. If None, the plot will not be saved.
- **Returns:**
    - None

**10. `generate_subsets(df_full)`**

- **Purpose:** Generate subsets of **`df_full`** based on unique state numbers. The function creates subsets of the full dataset, adjusting timestamps for continuity between different filenames and calculating volume differences and cumulative sum.
- **Parameters:**
    - **`df_full`** (DataFrame): DataFrame containing the full data.
- **Returns:**
    - **`subsets`** (dict): Dictionary containing subsets of **`df_full`** indexed by state numbers.

**11. `export_subsets_to_csv_and_plots(subsets, folderpath)`**

- **Purpose:** Export each subset DataFrame to a CSV file with the state number in the filename and save corresponding plots. The function organises processed data subsets into CSV files and generates plots for further analysis and reporting.
- **Parameters:**
    - **`subsets`** (dict): Dictionary of DataFrames indexed by state numbers.
    - **`folderpath`** (str): Path to the folder where CSV files and plots will be saved.
- **Returns:**
    - None

# HyTEST_Beta2_DataAnalysis.ipynb Documentation

## Data Information
Data types of the dataframe are:  

| label | no_quantity_label | type | is_time_series | group | physical_quantity | min | max | units | offset | scale |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Module pressure | Module | sensor | TRUE |  | pressure | 0 | 16.0 | bar | 0 | 1 |
| Rig ambient temperature | Rig ambient | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Water bath temperature | Water bath | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Vent. encl. ambient temperature | Vent. encl. ambient | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Coolant manifold temperature (in) | Coolant manifold (in) | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Coolant manifold temperature (out) | Coolant manifold (out) | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Module temperature (top) | Module (top) | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Module temperature (base) | Module (base) | sensor | TRUE |  | temperature | 0 | 100 | °C | 0 | 1 |
| Module flow rate | Module | sensor | TRUE |  | flow rate | 0 | 100 | nl/min | 0 | 1 |
| Electrolyser stack voltage | Electrolyser stack | sensor | TRUE |  | voltage | 0 | 100 | V | 0 | 1 |
| Electrolyser outlet pressure | Electrolyser outlet | sensor | TRUE |  | pressure | 0 | 100 | bar | 0 | 1 |
| Electrolyser stack temperature | Electrolyser stack | sensor | TRUE |  | temperature | 0 | 80 | °C | 0 | 1 |
| Electrolyser stack current | Electrolyser stack | sensor | TRUE |  | current | 0 | 100 | A | 0 | 1 |
| Electrolyser flow rate | Electrolyser | sensor | TRUE |  | flow rate | 0 | 100 | nl/min | 0 | 0.01666666667 |
| Module flow rate setpoint | Module setpoint | status | TRUE |  | flow rate | 0 | 100 | nl/min | 0 | 1 |
| Stack SoC | Stack | status | TRUE |  | SoC | 0 | 100 | % | 0 | 1 |
| Module SoC | Module | status | TRUE |  | SoC | 0 | 100 | % | 0 | 1 |
| Unit op mode |  | status | TRUE |  |  | 0 | 13 |  | 0 | 1 |
| Stack op mode |  | status | FALSE |  |  | 0 | 13 |  | 0 | 1 |
| Bank op mode |  | status | FALSE |  |  | 0 | 13 |  | 0 | 1 |
| Module op mode |  | status | FALSE |  |  | 0 | 13 |  | 0 | 1 |
| Module op mode request |  | status | FALSE |  |  | 0 | 13 |  | 0 | 1 |
| Module op mode sync |  | status | FALSE |  |  |  |  |  | 0 | 1 |
| Electrolyser production rate |  | status | FALSE |  |  | 60 | 100 | % |  |  |
| Remote control ready |  | status | FALSE |  |  |  |  |  |  |  |
| Remote control error |  | status | FALSE |  |  |  |  |  |  |  |
| Remote control enabled |  | control | FALSE |  |  |  |  |  |  |  |

One of the most important column is related to the OpMode states:

opModes = {
1: "Startup Maintenance",
2: "Standby",
4: "Cooling",
5: "Charge",
6: "Warming",
7: "Discharge",
8: "Flush Fill",
9: "Error",
10: "Shutdown",
12: "Vent",
13: "Flush Expel"}

From the chemistry point of view, what we are interested in the most is cooling-charge and warming-discharge, the other opModes are implemented to match scaled-up operations.

## Notebook Information
This script offers functionalities to process raw data, merge multiple dataframes, visualize data through scatter plots, and export processed data subsets along with corresponding plots for further analysis and reporting. Custom functions were implemented to filter the dataset according to the selected operation mode.

# ModularRig_ProcessRawData_V1.ipynb Documentation
## Data Information
| Filename | Example | Data type | No. Columns | Columns Header | Notes |
| --- | --- | --- | --- | --- | --- |
| ‘cycle No.’-YYYY-MM-DDThhhmm.csv | D47-2024-03-26T13h33.csv | 1 date-time
39 floats | 40 | runner_timestamp	
message_timestamp	
topic	
NTC1	
NTC2	
NTC3	
NTC4	
NTC5	
NTC6	
NTC7	
NTC8	
NTC9	
NTC10	
NTC11	
NTC12	
NTC13	
NTC14	
NTC15	
NTC16	
4-20mA Sensor 1	
4-20mA Sensor 2	
PID temperature	
Thermocouple limit temp	
MFC flow rate	
Strain 1	
Strain 2	
Strain 3	
Strain 4	
Strain 5	
Strain 6	
Strain 7	
Strain 8	
Strain 9	
Strain 10	
Strain 11	
Strain 12	
Solenoid States	
Heartbeat	
Sync Byte 1	
Sync Byte 2 | NTC* will be temperature data (not implemented yet 042024). Solenoid States: 0=idle, 5=abs, 58=des. |
|  |  |  |  |  |  |

## Notebook Information
This script processes raw data files in CSV format generated by a MogRig system. It calculates the cumulative volume of a specific flow rate parameter over time and optionally resamples the data to a specified time period. The processed data is then saved as new CSV files for further analysis or visualization.

**Installation:**

No specific installation steps are required beyond having Python installed on your system. This script utilizes standard libraries such as **`os`**, **`pandas`**, **`scipy`**, **`datetime`**, and **`pytz`**, which are commonly available with Python distributions. Ensure these libraries are installed, which can typically be done via pip, e.g., **`pip install pandas scipy`**.

**Usage:**

To use this script, provide the path to the folder containing the raw CSV files (**`folder_path`**). Optionally, you can specify a resampling period (**`resample_period`**) if you want the data to be resampled. The processed files will be saved in the specified output folder (**`output_folder`**). Run the script, and it will process the files and save the results accordingly.

**Code Logic:**

1. **File Processing**: The script first lists all CSV files in the specified folder and raises an error if no CSV files are found.
2. **Data Processing**: It then iterates through each CSV file, reads it into a pandas DataFrame, and performs the following operations:
    - Drops unnecessary columns ("Sync Byte 1", "Sync Byte 2", "Heartbeat").
    - Converts the 'runner_timestamp' column to a datetime index from unix format.
    - Calculates time differences (**`delta_t`**) and relative time (**`t`**) based on the 'message_timestamp' column.
    - Computes the cumulative volume using the trapezoidal rule integration method.
    - Optionally resamples the data if a resampling period is provided.
3. **File Saving**: For each processed DataFrame, it constructs a new filename based on the original filename and the timestamp of the first data point. It then saves the processed DataFrame as a new CSV file in the specified output folder.

**Configuration:**

- **Folder Path (`folder_path`)**: Specify the path to the folder containing the raw CSV files to be processed.
- **Resample Period (`resample_period`)**: Optionally specify a resampling period for the data. Set to **`None`** if no resampling is desired.
- **Output Folder (`output_folder`)**: Specify the folder where the processed CSV files will be saved. If the folder doesn't exist, it will be created automatically.

Ensure that the input CSV files follow the expected format for proper processing.
