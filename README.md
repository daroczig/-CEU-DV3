This is the R script/materials repository of the "[Data Visualization 4: Data Visualization in Production with Shiny](https://courses.ceu.edu/courses/2021-2022/data-visualization-4-data-visualization-production-shiny)" course in the 2021/2022 Spring term, part of the [MSc in Business Analytics](https://courses.ceu.edu/programs/ms/master-science-business-analytics) at CEU. For the previous edition, see [2019/2020 Spring](https://github.com/daroczig/CEU-DV3/tree/2019/2020), [2020/2021 Spring](https://github.com/daroczig/CEU-DV3/tree/2020/2021), and [2021/2022 Spring](https://github.com/daroczig/CEU-DV3/tree/2021/2022).

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
3. Install Slack, and join the #﻿ba-dv4-2022 channel in the `ceu-bizanalytics` group.

<details><summary>💪 RStudio Server installation steps</summary>

Follow steps from the [DE3 class](https://github.com/daroczig/CEU-R-prod), then:

```
sudo apt install -y r-cran-shinywidgets
sudo Rscript -e "library(devtools);withr::with_libpaths(new = '/usr/local/lib/R/site-library', install_github('dreamRs/particlesjs', upgrade = FALSE))"
```

</details>


## Schedule

300 mins on April 24, 2023:

* 13:30 - 15:10 session 1
* 15:10 - 15:40 break
* 15:40 - 17:20 session 2
* 17:20 - 17:40 break
* 17:40 - 19:20 session 3

150 mins on May 8 and 15, 2023:

* 13:30 - 15:00 session 1
* 15:00 - 15:15 break
* 15:15 - 16:15 session 2

### Day 1

#### Countdown-timer app

This week, we build a countdown timer app, similar to http://timer.solutions

1. App wireframe with a static UI: [6d31380](https://github.com/daroczig/CEU-DV3/commit/6d31380)

    <details><summary>ui.R</summary>

    ```r
    library(shiny)

    ui <- fluidPage(
        h1('Data Visualization 4'),
        h2('Data Visualization in Production with Shiny')
    )
    ```
    </details>

    <details><summary>server.R</summary>

    ```r
    library(shiny)
    server <- function(input, output) {

    }
    ```
    </details>

2. Move content generation to the backend: [b9a1e58](https://github.com/daroczig/CEU-DV3/commit/b9a1e58)

3. Add current time (to be later used as the baseline for the countdown timer): [4ca27b0](https://github.com/daroczig/CEU-DV3/commit/4ca27b0), update current time to be reactive: [563b0b8](https://github.com/daroczig/CEU-DV3/commit/563b0b8), show UNIX timestamp in human-friendly format: [4878717](https://github.com/daroczig/CEU-DV3/commit/4878717)

4. Actual countdown from the scheduled time: [2dccd33](https://github.com/daroczig/CEU-DV3/commit/2dccd33)

5. Add background: [5062a79](https://github.com/daroczig/CEU-DV3/commit/5062a79)

6. CSS tweaks to center the timer: [5797572](https://github.com/daroczig/CEU-DV3/commit/5797572)

7. Colorize timer when scheduled time is due based on current timezone: [f628ec6](https://github.com/daroczig/CEU-DV3/commit/f628ec6)

8. Move defaults to reactive values and let user updated via a modal window at [0da5d6c](https://github.com/daroczig/CEU-DV3/commit/0da5d6c)

9. Add time picker for the scheduled time: [b5bfb65](https://github.com/daroczig/CEU-DV3/commit/b5bfb65)

10. Better design for the settings button: [7ba46bd](https://github.com/daroczig/CEU-DV3/commit/7ba46bd)

11. Pass settings as URL parameters: [ec49a5c](https://github.com/daroczig/CEU-DV3/commit/ec49a5c)

12. Simplify `ui.R` by a single call to `renderUI`: [556fdbe](https://github.com/daroczig/CEU-DV3/commit/556fdbe)

13. Add support for timezone setting: [6eec991](https://github.com/daroczig/CEU-DV3/commit/6eec991)

14. Ask for consent before using the app: [2ba4494](https://github.com/daroczig/CEU-DV3/commit/2ba4494)

Final app:

<details><summary>ui.R</summary>

```r
library(shiny)
library(shinyWidgets)
library(particlesjs)

ui <- basicPage(
    tags$head(
        tags$link(rel = "stylesheet", type = "text/css", href = "app.css")
    ),
    uiOutput('app')
)
```
</details>

<details><summary>www/app.css</summary>

```css
.center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    background-color: #00000042;
    padding: 25px 30px;
    border-radius: 25px;
}

#settings_show {
    position: absolute;
    top: 25px;
    right: 25px;
    color: black;
}
```
</details>

<details><summary>server.R</summary>

```r
library(shiny)
library(shinyWidgets)
library(lubridate)

server <- function(input, output, session) {

    settings <- reactiveValues(
        title = 'Data Visualization 4',
        subtitle = 'Data Visualization in Production with Shiny',
        schedule = as.POSIXct('2023-04-24 13:30:00'),
        timezone = Sys.timezone()
    )

    output$countdown <- renderUI({
        invalidateLater(250)
        schedule_tz <- force_tz(settings$schedule, settings$timezone)
        color <- ifelse(schedule_tz > Sys.time(), 'black', 'red')
        remaining <- span(
            round(as.period(abs(schedule_tz - Sys.time()))),
            style = paste('color', color, sep = ':'))
        div(
            h1(settings$title),
            h2(settings$subtitle),
            h3('starts in'),
            h1(tags$b(remaining)),
            h4(paste('at', settings$schedule, settings$timezone)),
            class = 'center')
    })

    observeEvent(input$settings_show, {
        showModal(
            modalDialog(
                textInput("title", "Title", value = settings$title),
                textInput("subtitle", "Subtitle", value = settings$subtitle),
                airDatepickerInput("schedule", "Time", value = settings$schedule, timepicker = TRUE),
                selectInput("timezone", "Timezone", choices = OlsonNames(), selected = settings$timezone),
                footer = tagList(actionButton('settings_update', 'Update'))
            )
        )
    })
    observeEvent(input$settings_update, {
        settings$title <- input$title
        settings$subtitle <- input$subtitle
        settings$schedule <- as.POSIXct(input$schedule, tz = input$timezone)
        settings$timezone <- input$timezone
        removeModal()
    })

    observe({
        query <- parseQueryString(session$clientData$url_search)
        for (v in c('title', 'subtitle', 'schedule', 'timezone')) {
            if (!is.null(query[[v]])) {
                if (v == 'schedule') {
                    settings[[v]] <- as.POSIXct(query[[v]], tz = settings$timezone)
                } else {
                    settings[[v]] <- query[[v]]
                }
            }
        }
    })

    ## gdpr
    showModal(modalDialog(
        p('Click the below button you consent to ...'),
        footer = tagList(actionButton('consent', 'OK'))
    ))
    observeEvent(input$consent, {
        output$app <- renderUI({
            list(
                particles(),
                actionBttn('settings_show', 'Settings',
                           icon = icon('gear'),
                           style = 'material-circle'),
                uiOutput('countdown')
            )
        })
        removeModal()
    })

}
```
</details>

Further ideas to improve the app:

- get timezone from visitor's browser setting / locale
- configure theme (colors, layout, background etc)
- ads!!!

Questions & Answers:

* There's an icon next to the datetime picker not doing anything. What's that for?

> It's an action button that can trigger functions on the server side. If not needed, hide with setting the `addon` parameter of `airDatepickerInput` to `none`.

* The `DT` table is overflowing the provided width of the parent `column`. Why?

> By default, the rendered table doesn't have a scrollbar and so thus might be wider than preferred. To make sure it fits in the provided horizontal space, pass `options = list(scrollX = TRUE)` to `DT::renderDataTable`.

* How to use custom colors in an `infoBox`?

> You cannot :( There's a related GH ticket at https://github.com/rstudio/shinydashboard/issues/366

### Widgets

Mihaly Orsos shared materials at https://github.com/misrori/DV4-2023

## Day 2

Mihaly Orsos shared materials at https://github.com/misrori/DV4-2023

## Day 3

### gGPT

1. Create an `apps` folder in your home directory, then start a new Shiny web app there (e.g. `gGPT`).
2. Write an app that takes freetext input to analyze the `nycflights13` dataset by generating a relevant `ggplot2`, e.g. as seen on the below screenshot:

    ![2023-05-14_17-51](https://github.com/daroczig/CEU-DV4/assets/495736/e639db61-7588-405d-9790-858edd395f87)

    <details><summary>... .Renviron</summary>

    ```
    OPENAI_API_KEY=...
    ```
    </details>

    <details><summary>... ui.R</summary>

    ```r
    library(shiny)
    library(shinycssloaders)

    fluidPage(
      titlePanel('gGPT'),
      textAreaInput('question', 'Hey, what do you want to know about the nycflights dataset?',
                    height = '100px', width = '100%'),
      submitButton('Answer my question with a plot!', icon('image')),
      withSpinner(plotOutput('plot'), caption = 'Generating plot ...'),
      withSpinner(verbatimTextOutput('code'), caption = 'Hitting the OpenAI API for R code ...')
    )
    ```

    </details>

    <details><summary>... server.R</summary>

    ```r
    library(shiny)
    library(shinycssloaders)

    library(chatgpt)
    ## Sys.setenv(OPENAI_API_KEY = '...')

    ## chatgpt often forgets to load these pkgs
    library(nycflights13)
    library(dplyr)

    prompt <- "You are a data scientist using R to analyze data on the airline on-time data for all flights departing NYC in 2013 from the `nycflights13` R package, which provides the following datasets loaded:

    The `airlines` dataset: Look up airline names from their carrier codes.
    Data frame with columns:
    - carrier Two letter abbreviation.
    - name Full name.

    The `airports` dataset: Useful metadata about airports.
    A data frame with columns:
    - faa FAA airport code.
    - name Usual name of the aiport.
    - lat, lon Location of airport.
    - alt Altitude, in feet

    The `flights` dataset: On-time data for all flights that departed NYC (i.e. JFK, LGA or EWR) in 2013.
    Data frame with columns:
    - year, month, day Date of departure.
    - dep_time, arr_time Actual departure and arrival times (format HHMM or HMM), local tz.
    - sched_dep_time, sched_arr_time Scheduled departure and arrival times (format HHMM or HMM),
    local tz.
    - dep_delay, arr_delay Departure and arrival delays, in minutes. Negative times represent early
    departures/arrivals.
    - carrier Two letter carrier abbreviation. See airlines to get name.
    - flight Flight number.
    - tailnum Plane tail number. See planes for additional metadata.
    - origin, dest Origin and destination. See airports for additional metadata.
    - air_time Amount of time spent in the air, in minutes.
    - distance Distance between airports, in miles.
    - hour, minute Time of scheduled departure broken into hour and minutes.

    Do not forget to load the used packages.
    I want you to only reply with the R code inside one unique code block, and nothing else.
    Do not write explanations. Do not type commands unless I instruct you to do so.

    Provide the R code using ggplot2 to visualize:"

    function(input, output, session) {

      question <- reactive({
        shiny::validate(shiny::need(input$question != '', ''))
        paste(prompt, input$question)
      })

      code <- reactive({

        reset_chat_session()
        res <- ask_chatgpt(question())

        ## drop backtick fence around R code (cannot get ChatGPT do it)
        gsub('(^```\\{?[rR]?\\}?)|(```$)','', res)

      })

      output$code <- renderText(code())
      output$plot <- renderPlot(eval(parse(text = code())))

    }
    ```

    </details>

3. 💪 Install necessary packages:

    ```sh
    ## install R packages from CRAN via apt
    sudo apt update
    sudo apt install r-cran-nycflights13 r-cran-chatgpt

    ## needs more recent version than CRAN
    sudo Rscript -e "library(devtools);withr::with_libpaths(new = '/usr/local/lib/R/site-library', install_github('daattali/shinycssloaders', upgrade = FALSE))"
    ```

4. Try a few prompts:

    - Which day of the week has the worst delays?
    - Which are the top 5 most popular destinations?
    - Which are the top 5 most popular destinations? Order by popularity.
    - which are the top 25 shortest flights? order labels by distance
    - Analyze the distribution of delays to Los Angeles.
    - Which are the airports with at least 500 inbound flights being furthest from Chicago?
    - Draw a pie chart on the airlines based on the number of flights.
    - Is there any association between origin and delay?
    - Is there any association between origin and delay? Exclude delays over 500 minutes.

## Home Assignments

### Homework 1

Create a Shiny application as described below. You can use any dataset you like. If you have no preferred data source(s) to use, check the below ideas. In case of any question, reach out on Slack or open a GitHub ticket.

* You need to create a Shiny application with `shinydashboard` layout using a sidebar menu
* Add at least 3 input elements using at least 2 different methods (eg dropdown, slider, checkbox)
* Add at least 3 different UI elements (eg text, table, map, UI, info and valueboxes)
* Use `reactive` values

Some ideas for data source:

* `nycflights13` package
* https://github.com/rfordatascience/tidytuesday/
* https://www.kaggle.com/datasets

Deadline: May 8, 2023

## Suggested Reading

* Hadley Wickham (2021): Mastering Shiny. https://mastering-shiny.org/
* Business Science (2020): The Shiny AWS Book. https://business-science.github.io/shiny-production-with-aws-book/

## Home Assignment

To be uploaded.

## Contact

File a [GitHub ticket](https://github.com/daroczig/CEU-DV4/issues).
