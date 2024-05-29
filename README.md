This is the R script/materials repository of the "[Data Visualization 4: Building Dashboards](https://courses.ceu.edu/courses/2023-2024/data-visualization-4-building-dashboards)" course in the 2023/2024 Spring term, part of the [MSc in Business Analytics](https://courses.ceu.edu/programs/ms/master-science-business-analytics) at CEU. For the previous edition, see [2019/2020 Spring](https://github.com/daroczig/CEU-DV3/tree/2019/2020), [2020/2021 Spring](https://github.com/daroczig/CEU-DV3/tree/2020/2021), [2021/2022 Spring](https://github.com/daroczig/CEU-DV3/tree/2021/2022), and [2022/2023 Spring](https://github.com/daroczig/CEU-DV3/tree/2022/2023).

## Table of Contents

* [Syllabus](#syllabus)
* [Schedule](#schedule)
   * [Day 1](#day-1)
   * [Day 2](#day-2)
   * [Day 3](#day-3)
   * [Day 4](#day-4)
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

### Day 1

This week, we build a countdown timer app, similar to https://count-down-timer.eu.org

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
        schedule = as.POSIXct('2024-05-08 13:30:00'),
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

### Day 4

Start a new `t3a.medium` instance using the `dv4` AMI and `dv4` security group.
Please don't forget to set the Owner and Class tags!

Demo on localhost:

> which are the top 7 most frequent destinations from NY?

![](https://github.com/daroczig/CEU-DV4/assets/495736/d2e2bfe9-9bf4-451c-a868-09123a83e3a1
)

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
    ## Sys.setenv(OPENAI_API_KEY = ...)
    ## library(botor)
    ## botor(region = 'eu-west-1')
    ## Sys.setenv(OPENAI_API_KEY = ssm_get_parameter('openai'))


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

### Shiny Server

1. 💪 Install Shiny Server from https://rstudio.com/products/shiny/download-server/ubuntu/:

        sudo apt-get install gdebi-core
        wget https://download3.rstudio.org/ubuntu-18.04/x86_64/shiny-server-1.5.22.1017-amd64.deb
        sudo gdebi shiny-server-1.5.22.1017-amd64.deb
        rm shiny-server-1.5.22.1017-amd64.deb

3. Edit `site_dir` in `shiny-server.conf` to point to the `/home/$USERNAME/apps` folder and optionally also update the `run_as` directive to your username to avoid permissions issue:

        sudo mcedit /etc/shiny-server/shiny-server.conf
        sudo systemctl restart shiny-server

4. Visit Shiny Server on port 3838 from your browser

    ![](https://raw.githubusercontent.com/daroczig/CEU-R-prod/2018-2019/images/shiny-server.png)

5. 💪 Always keep logs -- set this in the Shiny Server config's top level & restart service as per https://docs.rstudio.com/shiny-server/#logging-and-analytics:

        preserve_logs true;

    Optionally, redirect all logs to the same file by injecting an environment variable in `/etc/systemd/system/shiny-server.service` by adding this line below the other `Environment=` line:

        Environment="SHINY_LOG_STDERR=1"

6. To keep an eye on logs (test with making a typo in the app on purpose):

        sudo tail -f /var/log/shiny-server.log

7. Add `ui.R` and `server.R` files (along with `global.R` and other stuff in the `www` folder) to the folder specified in step 3.

    Note, that Shiny Server has some limitations (eg scaling to multiple users, some headers removed by the Node.js wrapper) -- so you might consider either the Pro version, other RStudio products or eg the below-mentioned Shiny app manager daemon for using Shiny in production at scale.

8. 💪 Run behind a proxy to be able to access on the standard HTTP port (edit `/etc/nginx/sites-enabled/default`):

    ```sh
    http {

      map $http_upgrade $connection_upgrade {
        default upgrade;
        ''      close;
      }

      server {
        listen 80;

        rewrite ^/shiny$ $scheme://$http_host/shiny/ permanent;
        location /shiny/ {
          rewrite ^/shiny/(.*)$ /$1 break;
          proxy_pass http://localhost:3838;
          proxy_redirect / $scheme://$http_host/shiny/;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection $connection_upgrade;
          proxy_read_timeout 20d;
          proxy_buffering off;
        }
      }
    }
    ```


## Live Shiny

https://posit-dev.github.io/r-shinylive/

## Home Assignments

To be updated from week to week.

### Homework 1

1. Check the Shiny tutorial app by creating a new Shiny application in
RStudio's File/New menu, using the two files approach. Make sure that
you understand what and how the app is doing.

2. Create a new git repository on GitHub, and push these files
there. You will use this app as a starting point in the future
homeworks.

### Homework 2

- Create a Shiny web application with a dashboard layout, using the `shinydashboard` package.

- It must consist of at least two distinct UI widgets. These widgets could include sliders, dropdown menus, text inputs, radio buttons, etc.

- Include at least three server-side objects, with at least one of them being reactive. These objects should capture and process user inputs or data manipulation.

- Use `ggplot2` for data visualization based on the server-side data, which could be a bar plot, line plot, scatter plot, etc., depending on the nature of the data.
- Create an interactive plot using `plotly`. This plot should respond to user interactions, such as hovering, clicking, or selecting data points.
- Display a table that is used to create the visualizations.

### Homework 3

- Add at least two new UI widgets (e.g. sliders, dropdowns, text inputs) to the above-created application, and make sure those are integrated with your server logic.

- Add at least one new output based on the materials covered on week 3, e.g. `DT::datatable`, a map or network, `dygraph`, `infobox` or `valuebox`.

- Deploy your app to shinyapps.io as covered on week 3.

Submission: push the changes in your GitHub repo, and share the Shinyapps.io URL on Moodle.

## Suggested Reading

* Hadley Wickham (2021): Mastering Shiny. https://mastering-shiny.org/
* Business Science (2020): The Shiny AWS Book. https://business-science.github.io/shiny-production-with-aws-book/

## Contact

File a [GitHub ticket](https://github.com/daroczig/CEU-DV4/issues).
