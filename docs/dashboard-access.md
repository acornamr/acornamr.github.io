# Dashboard Access

The dashboard is available in three forms: a web app, a standalone app (Windows OS only), and an R package. Most users will use the standalone app on a regular basis to generate data in the acorn format. The web app could become useful during demonstration on laptops without the standalone and to quickly visualise data that has already be generated. Users familiar with R can install the `acorn-dashboard` package to run the app via the R command line.

## Standalone App

*(Windows OS only)*
A standalone version of the app can be downloaded and installed by following detailed instruction provided here: https://acornamr.net/docs/Updated_ACORN_app_installation_V1.1.pdf.

## Web App

*(Windows OS, macOS or Linux)*
The latest release of the web app can be accessed at https://moru.shinyapps.io/acorn2/.

## R Package

*(Windows, macOS or Linux)* 
The app can also be run as an R package.

- (Once) install R (4.1.0 or above) your PC 
- (At every new release of the ACORN dashboard) install the package: `remotes::install_github("acornamr/acorn-dashboard", ref = "master")`
- Launch the app: `acorn::run_app()`