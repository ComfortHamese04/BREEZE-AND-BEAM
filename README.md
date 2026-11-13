# Breeze & Beam

**Renewable energy forecasting, monitoring, and hybrid energy simulation.**

Breeze & Beam combines solar and wind generation forecasts with a simulated hydroelectric storage system to explore how renewable energy sources can work together to meet electricity demand. The project brings together machine learning in R, an interactive dashboard, role-based application features, and automated testing.

## Application preview

> **Add your preferred dashboard screenshot here.**

<!-- Upload your chosen screenshot and uncomment this line after updating the path:
![Breeze & Beam dashboard](images/dashboard.png)
-->

## Project overview

The project uses Stellenbosch in South Africa's Western Cape as the setting for a hybrid renewable-energy scenario. Its purpose is to explore a more sustainable energy supply by combining solar, wind, and hydroelectric resources, supported by forecasts and a monitoring interface.

Solar and wind generation vary with weather conditions. Breeze & Beam uses historical weather and generation data to forecast their output and support decisions about when a simulated hydroelectric system should store surplus power or generate power to cover a shortfall.

The system has three main components:

1. **Forecasting models:** separate XGBoost models for solar and wind generation.
2. **Monitoring and control dashboard:** generation information, account access, and administrator controls for the simulated hydro system.
3. **Simulation views:** graphs and tables comparing predicted and actual solar and wind generation over the year.

## Problem and objectives

The project addresses the challenge of coordinating variable renewable generation with electricity demand. A single renewable source may not consistently meet demand, so complementary sources and storage are considered together.

The objectives are to:

- Forecast solar and wind generation using historical weather and generation data.
- Compare predictions with recorded generation values.
- Present generation trends through graphs and tables.
- Use generation and demand information to support hydro charging and discharging decisions.
- Give administrators access to simulation controls while providing ordinary users with monitoring access.
- Test account-management behaviour with automated unit tests.

This is a software simulation and decision-support prototype. The documented design does not establish a commissioned energy installation or verified electricity supply to Stellenbosch.

## Hybrid energy design

| Component | Scenario described in the project | Role |
| --- | --- | --- |
| Solar farm | Simulated 40 MW installation | Provides generation dependent on solar conditions |
| Wind farm | Simulated 40 MW installation | Provides generation dependent on wind conditions |
| Hydroelectric system | Simulated 50–100 MW installation with an upper reservoir | Stores surplus energy through pumping and generates during shortfalls |

These capacities are scenario assumptions from the project documentation. Physical feasibility, storage duration, and real-world energy delivery require separate engineering validation.

### Hydro operating modes

**Charging or pumping mode:** When solar and wind output exceed current demand, part of the surplus is used to pump water into the upper reservoir.

**Discharging or generation mode:** When solar and wind output fall short of demand, stored water is released through the turbine to generate additional power, provided sufficient water is available.

This provides the central relationship between generation forecasts, demand, storage, and system control.

## Data workflow

### 1. Data inputs and preparation

The forecasting workflow reads preprocessed CSV files containing historical weather information and generation-related data. The documented weather inputs include:

- Solar irradiance.
- Wind speed.
- Temperature.
- Humidity.

The R ingestion example locates CSV files in a directory, uses an `-END HEADER-` marker to skip metadata, reads the files with `read_csv()`, and combines them with `bind_rows()`.

This produces a combined dataset for selecting predictors, constructing model inputs, and separating training and evaluation periods. The supplied document begins with preprocessed data; it does not specify the earlier missing-value, duplicate, or outlier treatments, nor does it name the exact meteorological provider.

### 2. Feature selection

Relevant weather variables were selected as predictors for solar and wind generation. Solar irradiance, wind speed, and humidity are specifically identified in the model-training description, with temperature also listed among the available inputs.

The purpose is to connect environmental conditions to expected renewable generation. The exact feature set for each model should be documented alongside its executable training script.

### 3. Time-based training and testing

The documented workflow separates the data chronologically:

| Dataset | Period | Purpose |
| --- | --- | --- |
| Training data | Up to and including 2023 | Fit the forecasting models |
| Test data | 2024 | Compare predictions with held-out recorded values |

Using a later period for testing evaluates how the models behave on data beyond their training period.

## Machine learning models

Breeze & Beam uses **XGBoost regression**, with separate models for solar and wind generation. The models predict numeric output values rather than eligibility labels or customer categories.

The documented R training example converts predictors into a matrix, wraps them in `xgb.DMatrix`, and fits a model with `xgb.train()`.

| Parameter | Documented setting |
| --- | --- |
| Objective | `reg:squarederror` |
| Evaluation metric | `rmse` |
| Maximum tree depth | `6` |
| Learning rate (`eta`) | `0.1` |
| Boosting rounds | `100` |

The report describes hyperparameter tuning, but it provides one configuration rather than a search history or comparative tuning results.

### Predictions and output files

Predictions were generated for the 2024 test data and exported to CSV. The documented output structure contains:

| Field | Meaning |
| --- | --- |
| Date / timestamp | Observation time |
| Actual | Recorded target value |
| Predicted | Corresponding model prediction |

Separate output files are described for solar and wind. The report states that the CSVs contain hourly records, which feed the dashboard workflow. The simulation pages present daily generation comparisons across the year.

The document refers both to generation predictions and to applying source-specific formulas to CSV values. The exact forecast target, conversion formulas, aggregation rules, and units should be checked against the source code before interpreting the outputs as physical energy totals. MW expresses power; energy over a period would normally be expressed in MWh.

## Dashboard features

### Login and registration

The application provides account-based access with:

- Email and password login.
- A Remember Me option that remembers the email address.
- Registration using a username, email, password, and password confirmation.
- Input validation and feedback for invalid registration details.
- Database-backed account creation after successful validation.
- Navigation between login, registration, and the main dashboard.

### Administrator and user views

| Capability | Administrator | User |
| --- | --- | --- |
| View generation information and graphs | Yes | Yes |
| Monitor the displayed system state | Yes | Yes |
| Access manual floodgate controls | Yes | No |
| Access manual pump controls | Yes | No |

The documented user interface hides the manual controls from ordinary users. Server-side or application-layer authorisation would need to be verified in the source code separately.

### Generation simulation views

Solar and wind have separate pages with:

- Line graphs comparing predicted and actual daily generation across the year.
- Tables containing predicted and actual values.
- Navigation between the solar and wind views.

These views make it easier to inspect seasonal patterns and differences between forecasts and recorded values.

> **Add your preferred solar and wind simulation screenshots here.**

<!--
![Solar generation comparison](images/solar-simulation.png)
![Wind generation comparison](images/wind-simulation.png)
-->

## Automated testing

The dashboard was developed using Visual Studio. The documentation includes C# unit-test examples using **xUnit** to exercise the `UserManagement` class, with simulated database data supplied through test helpers and mocked database access.

| Area | Documented testing focus |
| --- | --- |
| `Login` | Valid credentials, invalid passwords, and a nonexistent email address |
| `CheckAccountExists` | Account-existence checks |
| `CheckEmailFormat` | Email-format validation |
| `RegisterNewUser` | New-user registration behaviour |

The login examples follow the Arrange–Act–Assert pattern: prepare credentials and mocked responses, call the method, and assert the expected result. This allows account logic to be exercised independently of a live database.

The supplied report contains test examples, but it does not provide a complete test-run summary, pass count, or coverage percentage.

## Results and project outcomes

The documented implementation demonstrates:

- Separate XGBoost forecasting workflows for solar and wind.
- A time-based evaluation design using 2024 as the test period.
- CSV outputs containing predictions and recorded values for comparison.
- Integration of prediction data into dashboard and simulation views.
- Administrator and ordinary-user interfaces with different control access.
- A simulated hydro storage design with pumping and generation modes.
- Automated account-management test examples.

The report does not provide numerical RMSE, MAE, or R² scores. Model accuracy therefore cannot be quantified from this document alone. It also does not measure real-world emissions reductions, energy savings, operational uptime, or the ability to meet a town's actual demand.

The main demonstrated outcome is a connected prototype that combines forecasting, visualisation, user management, and energy-system simulation.

## Technologies

| Technology | Use |
| --- | --- |
| R | Data ingestion, model preparation, training, predictions, and CSV export |
| XGBoost | Solar and wind regression models |
| `read_csv()` and `bind_rows()` | Reading and combining CSV data in the R examples |
| C# | Account-management logic and unit-test examples |
| Visual Studio | Dashboard development environment |
| xUnit | Automated tests |
| CSV | Exchange of historical inputs and prediction outputs |
| Database-backed account storage | Registration and account lookup; database engine not specified in the supplied report |

## Running the project

Exact setup commands depend on the repository's solution file, target framework, R scripts, database configuration, and data paths. These are not included in the supplied implementation report.

The documented execution sequence is:

1. Prepare the historical weather and generation CSV files.
2. Run the R ingestion and preparation workflow.
3. Fit the separate solar and wind XGBoost models using the training period.
4. Generate predictions for the 2024 test period and export the results.
5. Configure the dashboard to read the required outputs and access its account database.
6. Launch the application and inspect the generation and simulation views.
7. Run the account-management unit tests in the development environment.

Add repository-specific installation and launch commands once the source files and configuration requirements are available.

## Future improvements

- Publish the complete data-preparation process, data sources, schemas, and units.
- Report RMSE, MAE, and R² separately for solar and wind, with comparisons against simple forecasting baselines.
- Evaluate performance across seasons and different forecast horizons.
- Document the relationship between weather predictions, generation formulas, and displayed output.
- Validate reservoir capacity, hydraulic head, efficiency, and power-to-energy calculations.
- Verify access controls beyond hiding buttons in the interface.
- Extend tests to generation calculations, storage-state transitions, CSV loading, and role permissions.
- Add reproducible setup instructions, dependency versions, and a complete test-run summary.

## Documentation basis

This README is based on the supplied implementation and testing report. It describes the documented system and distinguishes implemented examples from measurements or setup details that still require the source repository.
