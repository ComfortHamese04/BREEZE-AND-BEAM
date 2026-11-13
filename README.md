<img width="1276" height="1440" alt="image" src="https://github.com/user-attachments/assets/47d027c5-1b81-4d60-a1c0-c978ccc6919a" /># Breeze & Beam
### Renewable Energy Forecasting and Management System

Breeze & Beam is a renewable energy forecasting and simulation project that combines solar, wind, and hydroelectric resources. It uses machine learning to forecast solar and wind generation, presents energy information through an interactive dashboard, and demonstrates how hydroelectric storage can support changing electricity demand.

The project brings together data preparation, predictive modelling, visualisation, account management, administrator controls, and automated testing.

## Project Overview

Breeze & Beam uses Stellenbosch in South Africa’s Western Cape as the setting for a hybrid renewable energy simulation.

Solar and wind generation depend on changing weather conditions. The system explores how these resources can work together with hydroelectric storage to balance electricity generation and demand.

The solution consists of three main components:

1. **Prediction models:** Separate XGBoost models for forecasting solar and wind generation.
2. **Monitoring dashboard:** An interface for viewing generation information, with different functionality for administrators and ordinary users.
3. **Simulation views:** Graphs and tables comparing predicted and actual generation throughout the year.

## Problem Statement

Variable weather conditions make it difficult to rely on a single renewable energy source. Solar generation changes with sunlight, while wind generation depends on wind conditions.

Breeze & Beam explores a combined approach in which surplus renewable generation supports water storage, and stored water supports electricity generation when solar and wind output are insufficient.

## Project Objectives

- Prepare historical weather and generation data for modelling.
- Develop separate forecasting models for solar and wind generation.
- Compare predicted generation with recorded values.
- Visualise generation trends through graphs and tables.
- Support simulated hydroelectric pumping and generation decisions.
- Provide separate administrator and user dashboard experiences.
- Implement account registration and login.
- Test account-management functionality using automated unit tests.

## Hybrid Energy System

| Component | Simulated Capacity | Purpose |
| --- | --- | --- |
| Solar farm | 40 MW | Generate electricity from solar resources |
| Wind farm | 40 MW | Generate electricity from wind resources |
| Hydroelectric system | 50–100 MW | Support storage and generation during supply fluctuations |

These capacities represent the project’s simulation assumptions rather than a commissioned energy installation.

### Charging Mode

When solar and wind generation exceed demand, part of the surplus electricity is used to pump water into an upper reservoir.

### Discharging Mode

When solar and wind generation are insufficient, stored water is released through a turbine to generate additional electricity, provided sufficient water is available.

## Data Preparation

### Data Inputs

The forecasting workflow uses preprocessed CSV files containing historical weather and generation-related information.

The documented weather variables include:

- Solar irradiance.
- Wind speed.
- Temperature.
- Humidity.

### Data Loading and Combination

The R workflow reads multiple CSV files from a directory and combines them into a dataset for analysis.

The process includes:

1. Identifying the CSV files.
2. Locating the `-END HEADER-` marker to skip file metadata.
3. Reading the data using `read_csv()`.
4. Combining the files using `bind_rows()`.
5. Preparing predictors and target values for model training.

The implementation document begins with preprocessed files. Earlier missing-value, duplicate, and outlier treatments are not specified.

### Feature Selection

Weather variables were selected to support the prediction of solar and wind generation. The training description identifies solar irradiance, wind speed, and humidity as relevant predictors, with temperature also available in the input data.

### Training and Testing Split

The data was divided chronologically:

| Dataset | Period | Purpose |
| --- | --- | --- |
| Training data | Up to and including 2023 | Train the forecasting models |
| Test data | 2024 | Evaluate predictions against recorded values |

This approach evaluates the models on a later period than the data used for training.

## Machine Learning

### XGBoost Regression

Separate XGBoost regression models were developed for solar and wind generation.

The training workflow involved:

1. Selecting the relevant predictors.
2. Converting the training features into a matrix.
3. Creating an `xgb.DMatrix` containing predictors and target values.
4. Training the model using `xgb.train()`.
5. Generating predictions for the test period.
6. Exporting predictions alongside recorded values.

### Documented Model Configuration

| Parameter | Value |
| --- | --- |
| Objective | `reg:squarederror` |
| Evaluation metric | `rmse` |
| Maximum tree depth | `6` |
| Learning rate | `0.1` |
| Boosting rounds | `100` |

The implementation report describes model tuning but does not include a complete tuning history or numerical comparison between configurations.

### Prediction Outputs

Separate CSV outputs were produced for solar and wind.

| Field | Description |
| --- | --- |
| Date / timestamp | Time associated with the observation |
| Actual | Recorded target value |
| Predicted | Model prediction |

The documented CSVs contain hourly records, while the simulation views present daily comparisons across the year.

The report also refers to source-specific generation formulas. The exact conversion formulas and units should be checked against the implementation when interpreting the outputs.

## Application Features

<!--
SCREENSHOT INSTRUCTIONS

Create an images folder in the root of your repository.
Upload your screenshots using the filenames below, or update each image
path to match your chosen filename and extension.

images/login-page.png
images/register-page.png
images/admin-dashboard.png
images/user-dashboard.png
images/solar-simulation.png
images/wind-simulation.png

The image links below will display after the matching files are uploaded.
-->

### Login Page

The login page allows existing users to access the application using their email address and password.

Features include:

- Email and password inputs.
- A **Remember Me** option that remembers the email address.
- Navigation to the registration page.
- Access to the dashboard appropriate to the account’s role.


<img width="1276" height="1440" alt="image" src="https://github.com/user-attachments/assets/08d1e326-510f-4f4f-9a63-bc93811fc920" />


### Registration Page

New users can create an account by entering:

- Username.
- Email address.
- Password.
- Password confirmation.

The application validates the supplied details and provides feedback for invalid inputs. After successful validation, the account information is stored in the database and the user returns to the login page.

A **Cancel** button returns to login without creating an account.

<img width="539" height="562" alt="image" src="https://github.com/user-attachments/assets/3cc09da3-4818-4bad-9ee8-44bb8c01d7d1" />


### Administrator Dashboard

The administrator dashboard provides generation information and access to the simulated hydroelectric system’s manual controls.

Administrators can:

- View generation information and graphs.
- Monitor the displayed system state.
- Access manual floodgate controls.
- Access manual pump controls.

These controls support interaction with the simulated pumping and generation processes.

<img width="1276" height="1440" alt="image" src="https://github.com/user-attachments/assets/ab0d4ccc-259a-4299-81a5-03aaec808aa6" />


### User Dashboard

The user dashboard provides access to the same generation information and graphs for monitoring.

Ordinary users can:

- View generation information.
- Inspect dashboard graphs.
- Monitor the displayed system state.

Manual floodgate and pump controls are hidden from the ordinary-user interface.
<img width="1271" height="1440" alt="image" src="https://github.com/user-attachments/assets/d7f6ee99-72db-4592-86e7-c91416f47577" />



### Role Comparison

| Feature | Administrator | User |
| --- | --- | --- |
| View generation information | Yes | Yes |
| View dashboard graphs | Yes | Yes |
| Monitor displayed system state | Yes | Yes |
| Access manual floodgate controls | Yes | No |
| Access manual pump controls | Yes | No |

### Solar Simulation Page

The solar simulation page presents:

- A line graph comparing predicted and actual daily generation.
- A table containing the corresponding values.
- Generation comparisons across the year.
- Navigation to the wind simulation page.

This view supports inspection of solar generation patterns and differences between predictions and recorded values.

<img width="1253" height="125" alt="image" src="https://github.com/user-attachments/assets/e74c2ee9-5f7b-450c-96bf-15a2b0abef14" />
<img width="1380" height="675" alt="image" src="https://github.com/user-attachments/assets/60ba8baa-df78-43a4-8fc2-92033577fd0f" />
<img width="1378" height="513" alt="image" src="https://github.com/user-attachments/assets/7172612c-f86a-4640-a6d6-a1bc304e229d" />



### Wind Simulation Page

The wind simulation page provides:

- A line graph comparing predicted and actual daily wind generation.
- A table containing the corresponding values.
- Generation comparisons across the year.
- Navigation back to the solar simulation page.

This view supports inspection of wind generation patterns and differences between predictions and recorded values.
<img width="1253" height="125" alt="image" src="https://github.com/user-attachments/assets/c5f05dc3-554c-4a25-8a58-3b7f1c94fa11" />
<img width="1253" height="611" alt="image" src="https://github.com/user-attachments/assets/1693fade-5083-4d38-87b0-df9ba9da64ce" />
<img width="1253" height="467" alt="image" src="https://github.com/user-attachments/assets/eebea739-72e5-4eb7-a952-45d2ac8ed9a2" />



## Application Workflow

1. Open the login page.
2. Register an account if required.
3. Sign in using an email address and password.
4. Access the dashboard appropriate to the account’s role.
5. View generation information and graphs.
6. Use administrator controls where available.
7. Inspect solar and wind simulation views to compare predicted and recorded generation.

## Automated Testing

The dashboard was developed in Visual Studio. Automated tests were written using **xUnit** to exercise the `UserManagement` class.

Mocked database access and helper methods were used to provide test data without depending on a live database.

| Method | Testing Focus |
| --- | --- |
| `Login` | Valid credentials, invalid passwords, and nonexistent email addresses |
| `CheckAccountExists` | Account-existence checks |
| `CheckEmailFormat` | Email-format validation |
| `RegisterNewUser` | Account-registration behaviour |

The login tests follow the Arrange–Act–Assert pattern:

1. **Arrange:** Prepare test inputs and mocked database responses.
2. **Act:** Call the method being tested.
3. **Assert:** Check whether the returned result matches the expected behaviour.

The documentation includes test examples but does not provide a complete pass count or coverage percentage.

## Technologies Used

| Technology | Purpose |
| --- | --- |
| R | Data preparation, model training, predictions, and CSV export |
| XGBoost | Solar and wind regression models |
| `read_csv()` | Reading CSV data |
| `bind_rows()` | Combining datasets |
| C# | Account-management logic and unit-test examples |
| Visual Studio | Application development |
| xUnit | Automated testing |
| CSV | Historical data and prediction output exchange |
| Database-backed storage | Account registration and lookup |

## Results and Outcomes

The documented implementation demonstrates:

- Separate forecasting workflows for solar and wind generation.
- A chronological training and testing approach.
- Prediction outputs containing recorded and predicted values.
- Integration of prediction data into dashboard and simulation views.
- Separate administrator and ordinary-user interfaces.
- Manual hydro controls within the administrator interface.
- A simulated pumping and generation design.
- Automated account-management test examples.

The project demonstrates how forecasting, visualisation, application development, and energy-system simulation can be combined into one prototype.

Numerical RMSE, MAE, and R² results are not included in the supplied implementation report. Real-world energy savings, emissions reductions, and operational performance have also not been measured in that document.

## Future Improvements

- Document the complete data-cleaning process and original data sources.
- Report separate forecasting metrics for solar and wind.
- Compare XGBoost against simple forecasting baselines.
- Evaluate model performance across seasons.
- Document generation formulas, units, and aggregation rules.
- Validate hydro storage capacity and energy calculations.
- Verify role permissions within the underlying application logic.
- Extend automated testing to generation calculations and storage transitions.
- Add repository-specific installation and launch instructions.
