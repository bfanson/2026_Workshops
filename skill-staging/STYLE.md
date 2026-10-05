# R Coding Style Guide — Ben Fanson / ARI

This file captures personal R coding conventions to be followed by AI agents
when writing or modifying R code in this project.

---

## Pipes

- Use the **magrittr pipe** `%>%` (not the base pipe `|>`)
- Place `%>%` at the **end** of the line, not the start of the next line
- Chain across multiple lines with 2-space indentation:
  ```r
  x <- ds_fish %>%
    filter(!is.na(site_id)) %>%
    select(fish_id, site_id)
  ```

---

## Naming conventions

### Functions
- Use `camelCase` for function names: `getSR()`, `fmtSrRatio()`, `assignDateOtolith()`
- Use a descriptive **verb + noun** pattern: `assignBasin()`, `convertSF()`, `runUpload()`
- Prefix families of related functions consistently: `fmt*`, `db_*`, `st_*`, `sr_*`

### Variables and objects
- Use `snake_case` for variable/object names
- Apply meaningful **type prefixes**:

  | Prefix | Type |
  |--------|------|
  | `ds_`  | data frame / tibble |
  | `sf_`  | `sf` spatial object |
  | `sn_`  | `sfnetworks` object |
  | `v_`   | vector |
  | `dt_`  | date or datetime |
  | `d_`   | function argument that is a data frame |
  | `path_`| file/directory path string |
  | `n_`   | count/integer scalar |
  | `id_`  | logical flag or ID string |

- Database accessor functions follow `db_*()` naming: `db_fish()`, `db_mc()`
- Spatial layer functions follow `st_*()` naming: `st_site()`, `st_river()`

---

## Spacing and alignment

- Spaces **inside** parentheses for function calls:
  ```r
  filter( ds_fish, !is.na(site_id) )
  select( x, fish_id, site_id )
  ```
- Align similar assignments using spaces so that `<-` signs line up:
  ```r
  db_age    <- function() readRDS( here(file.path(path_db, 'db_age.RDS'   )) )
  db_mc     <- function() readRDS( here(file.path(path_db, 'db_mc.RDS'    )) )
  db_site   <- function() readRDS( here(file.path(path_db, 'db_site.RDS'  )) )
  ```
- 2-space indentation throughout

---

## Strings and quoting

- Prefer **single quotes** `'string'` over double quotes `"string"`
- Use `glue()` for string interpolation: `glue('{year}-{month}-{day}')`

---

## File headers

Every R script begins with a standard comment block:

```r
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
#  Purpose:     <brief description>                     #
#  Project ID:  2021_EWKR                               #
#  Date:        <YYYYMMMDD>                             #
#  Author:      Ben Fanson                              #
#  Company:     ARI                                     #
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
```

---

## Section headers

Use `####` on both sides for section headers. Precede major sections with a
line of underscores:

```r
#__________________________________________________________________________________
#--- Section 1: description of section ####

#### helper functions ####
```

---

## Comments

- Inline comments use `#` with a single space: `# this is a comment`
- Use `# packages: pkg1, pkg2` at the top of a function if it has non-obvious dependencies
- Comment style for grouping: `#--- group label ---#`
- Avoid restating the code in comments — only comment when the *reason* isn't obvious

---

## Assignment operator

- Always use `<-` (never `=`) for assignment
- Use `=` only inside function argument lists

---

## Paths

- Use `here()` for all project-relative file paths
- Combine with `file.path()` for subdirectories:
  ```r
  readRDS( here(file.path(path_db, 'db_fish.RDS')) )
  ```

---

## Package-specific rules (for R package development)

These override the above where there is a conflict:

- **No `library()` calls** inside package functions — use `pkg::fn()` or
  `@importFrom pkg fn` in roxygen documentation
- **No `source()` calls** inside package functions
- Every exported function must have **roxygen2 documentation**
- Internal helper functions do not need roxygen docs
- Tests go in `tests/testthat/test-<filename>.R`
