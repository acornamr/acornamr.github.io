# Dashboard Installation

The app is available in three forms: a web app, a standalone app (Windows OS only), and an R package.

## Web App

*(Windows OS, macOS or Linux)*
The latest release of the web app can be accessed at https://moru.shinyapps.io/acorn2/.

## Standalone App

*(Windows OS only)*
A standalone version of the app can be downloaded from [this repository' releases section](https://github.com/acornamr/acorn-dashboard/releases).


## R Package

*(Windows, macOS or Linux)* 
The app can also be run as an R package.

- (Once) install R (4.1.0 or above) your PC 
- (At every new release of the ACORN dashboard) install the package: `remotes::install_github("acornamr/acorn-dashboard", ref = "master")`
- Launch the app: `acorn::run_app()`