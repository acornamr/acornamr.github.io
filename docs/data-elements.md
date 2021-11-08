# Data Elements

The dashboard is concerned with the following data elements:

- `ACORN2_lab_codes.xlsx` shared across sites; stored in /shared-acornamr bucket.
- several `encrypted_cred_xxYYYY_zzz.rd` with credential informations; stored in /shared-acornamr bucket.
- Every site has a `ACORN2_lab_data_dictionary_xxYYY_TABULAR.xlsx` and/or `ACORN2_lab_data_dictionary_xxYYY_WHONET.xlsx`; stored in site bucket.
- Every site has several `.acorn` and `.acorn_non_anoymised` files; stored in site bucket.

Other data elements by the user either throuh a connection (REDCap data) or via an upload (Lab data). But those elements are not kept or stored by the dashboard.

## .acorn Files

.acorn files can be read and loaded in memory with the R command `base::load`.

for example `load(file = "SITE_2023-08-24_01H59.acorn")` loads a R list containing the following elements:

- `redcap_f01f05_dta`: one patient enrolment per row. patient_id is hashed.
- `redcap_hai_dta`: one survey element per row.
- `lab_dta`: one row per isolate as per the lab file provided on generation of the .acorn file.
- `corresp_org_antibio`: matrix of antibiotics and bugs.
- `acorn_dta`: one isolate per row. Contains only isolates that can be linked to a patient enrolment in `redcap_f01f05_dta`. patient_id is hashed.
- `data_dictionary`: all data of the site lab data dictionary file that has been used during the generation of the .acorn file.
- `lab_code`: all data of the ACORN2 lab code file that has been used during the generation of the .acorn file.
- `meta`: metadata collected on generation of the .acorn file.

## Non Anonymised .acorn Files

With each file NAME.acorn, a NAME.acorn_non_anonymised is also created.

This NAME.acorn_non_anonymised can also be loaded with the same command `load(file = "NAME.acorn_non_anonymised")` (supposing the file is located in the current working directory).

The file contains the same list as NAME.acorn, but with patient ids NOT hashed in `redcap_f01f05_dta`, `lab_dta`, and  `acorn_dta` elements.

The non_anonmyised `.acorn` files must be handled carefully and securely, as they contain raw patient identifiers: these files may be used by local investigators to link data / bacterial isolates to additional research projects / quality improvement activities. Please [contact the ACORN team](contact.md) for more information.
