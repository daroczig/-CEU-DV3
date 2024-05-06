This is the R script/materials repository of the "[Data Visualization 4: Building Dashboards](https://courses.ceu.edu/courses/2023-2024/data-visualization-4-building-dashboards)" course in the 2023/2024 Spring term, part of the [MSc in Business Analytics](https://courses.ceu.edu/programs/ms/master-science-business-analytics) at CEU. For the previous edition, see [2019/2020 Spring](https://github.com/daroczig/CEU-DV3/tree/2019/2020), [2020/2021 Spring](https://github.com/daroczig/CEU-DV3/tree/2020/2021), [2021/2022 Spring](https://github.com/daroczig/CEU-DV3/tree/2021/2022), and [2022/2023 Spring](https://github.com/daroczig/CEU-DV3/tree/2022/2023).

## Table of Contents

* [Syllabus](#syllabus)
* [Schedule](#schedule)
   * [Day 1](#day-1)
   * [Day 2](#day-2)
   * [Day 3](#day-3)
* [Home Assignment](#home-assignment)
* [Contact](#contacts)

## Syllabus

Please find in the `syllabus` folder of this repository.

## Technical Prerequisites

1. You need a laptop with any operating system and stable Internet connection.
2. Either install [R](https://cran.r-project.org) and [RStudio Desktop (open-source)](https://www.rstudio.com/products/rstudio/download) on your laptop, or use the shared RStudio Server already configured with all required software.
3. Install Slack, and join the #﻿ba-dv4-2023 channel in the `ceu-bizanalytics` group.

<details><summary>💪 RStudio Server installation steps</summary>

Follow steps from the [DE3 class](https://github.com/daroczig/CEU-R-prod), then:

```
sudo apt install -y r-cran-shinywidgets
sudo Rscript -e "library(devtools);withr::with_libpaths(new = '/usr/local/lib/R/site-library', install_github('dreamRs/particlesjs', upgrade = FALSE))"
```

</details>

## Schedule

150 mins on May 8, 15, 22, and 29, 2024:

* 13:30 - 15:00 session 1
* 15:00 - 15:15 break
* 15:15 - 16:15 session 2

Course materials will be updated from week-to-week.

## Live Shiny

https://posit-dev.github.io/r-shinylive/

## Home Assignments

To be updated.

## Suggested Reading

* Hadley Wickham (2021): Mastering Shiny. https://mastering-shiny.org/
* Business Science (2020): The Shiny AWS Book. https://business-science.github.io/shiny-production-with-aws-book/

## Contact

File a [GitHub ticket](https://github.com/daroczig/CEU-DV4/issues).
