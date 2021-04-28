This is the R script/materials repository of the "[Data Visualization 4: Data Visualization in Production with Shiny](https://courses.ceu.edu/courses/2020-2021/data-visualization-4-data-visualization-production-shiny)" course in the 2019/2020 Spring term, part of the [MSc in Business Analytics](https://courses.ceu.edu/programs/ms/master-science-business-analytics) at CEU. For the previous edition, see [2019/2020 Spring](https://github.com/daroczig/CEU-DV3/tree/2019-2020).

## Table of Contents

* [Syllabus](#syllabus)
* [Schedule](#schedule)
   * [Day 1-2](#day-1-2)
   * [Day 3](#day-3)
   * [Day 4](#day-4)
* [Home Assignment](#home-assignment)
* [Contact](#contacts)

## Syllabus

Please find in the `syllabus` folder of this repository.

## Schedule

4 x 150 mins between April 26 - 29, 2021.

### Day 1-2

Introduction to Shiny by Mihaly Orsos: https://github.com/misrori/CEU-DV-4-2020

### Day 3

Overview on the previous week's materials by Mihaly Orsos:

* basics of `ui.R` and `server.R`
* HTML tags
* reactive functions
* action button
* `renderUI`
* UI layouts
  * basic UI elements
  * Shiny themes
  * `shinydashboard`
* Password-protected applications
* Google Analytics

#### Countdown-timer app

This week, we build a countdown timer app, similar to http://count-down-timer.tk

1. App wireframe with a static UI:

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

2. Move content generation to the backend: [f0b53ee](https://github.com/daroczig/CEU-DV3/commit/f0b53ee0da9a816a44448ab8f170c5ca7d46e2f2)

3. Add current time (to be later used as the baseline for the countdown timer): [5dd6724](https://github.com/daroczig/CEU-DV3/commit/5dd67247fdf3a53312737057273c31fb1dbe11a8)

4. Actual countdown from the scheduled time: [5f91927](https://github.com/daroczig/CEU-DV3/commit/5f9192722314771e8898a4b1a800c8d9ef62a896)

5. Add background: [79fbe20](https://github.com/daroczig/CEU-DV3/commit/79fbe20aceb6234a563dea76eb95a19eca7fbc02)

6. CSS tweaks to center the timer: [d1df52d](https://github.com/daroczig/CEU-DV3/commit/d1df52d999ae70b4e6eac76270646733b2bc1529)

7. Colorize timer when scheduled time is due: [b74aead](https://github.com/daroczig/CEU-DV3/commit/b74aead0493ee094515d1741e58cc536f026261e)

8. Show time-zone information: [c23d1fe](https://github.com/daroczig/CEU-DV3/commit/c23d1fe3013a04d78636c658bd965199e52bb0dd)

9. Move defaults to reactive values and let user updated via a modal window: [d9cfb6c](https://github.com/daroczig/CEU-DV3/commit/d9cfb6c0121bd2a721440e6f33570c28633580d3)

10. Get rid of the `settingsModal` function and just pass the `modalDialog` to `showModal`: [acb815d](https://github.com/daroczig/CEU-DV3/commit/acb815db0ea3c6d9b2510927a407b738ff1e87c0#diff-f2a835a7de5549894c0b86022978173cL25)

11. Add time picker for the scheduled time: [acb815d](https://github.com/daroczig/CEU-DV3/commit/acb815db0ea3c6d9b2510927a407b738ff1e87c0#diff-f2a835a7de5549894c0b86022978173cL29)

12. Better design for the settings button: [4b6e7e3..d0efbe7](https://github.com/daroczig/CEU-DV3/compare/acb815d..d0efbe7)

13. Pass settings as URL parameters: [dbb3ed8](https://github.com/daroczig/CEU-DV3/commit/dbb3ed816d0994e7abe447ba806af8c241ee9dfc)

14. Simplify `ui.R` by a single call to `renderUI`: [2d468e7](https://github.com/daroczig/CEU-DV3/commit/2d468e706285724f5355319d64ad8da52e9ec2ed)

15. Fix subtitle and schedule settings update: [b416dd5](https://github.com/daroczig/CEU-DV3/commit/b416dd5903ec0bf299dd2aa656e3fa5ff37ed972)

16. Add support for timezone setting: [8a9be0e](https://github.com/daroczig/CEU-DV3/commit/8a9be0e32cd829941d8bbeb8a85259f5d1089b48)

17. Ask for consent before using the app: [798dd68](https://github.com/daroczig/CEU-DV3/commit/798dd68b66ba22569a292720188bf9ff0711cb0d)

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
    padding: 25px 50px;
    border-radius: 25px;
}

.datepicker{
    z-index:1151 !important;
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
        schedule = '2021-04-28 13:30:00',
        timezone = Sys.timezone()
    )

    output$countdown <- renderUI({
        invalidateLater(500)
        schedule <- ymd_hms(settings$schedule, tz = settings$timezone)
        color <- ifelse(schedule > Sys.time(), 'black', 'red')
        remaining <- span(
            round(as.period(abs(schedule - Sys.time()))),
            style = paste('color', color, sep = ':'))
        div(
            h1(settings$title),
            h2(settings$subtitle),
            h3('starts in'),
            h1(tags$b(remaining)),
            h4(paste('at', settings$schedule, settings$timezone)),
            class = 'center')
    })

    ## load settings from URL query params
    observe({
        query <- parseQueryString(session$clientData$url_search)
        for (v in c('title', 'subtitle', 'schedule', 'timezone')) {
            if (!is.null(query[[v]])) {
                settings[[v]] <- query[[v]]
            }
        }
    })

    ## override settings from modal
    observeEvent(input$settings_show, {
        showModal(modalDialog(
            textInput(
                'title', 'Title',
                value = settings$title),
            textInput(
                'subtitle', 'Subtitle',
                value = settings$subtitle),
            airDatepickerInput(
                'schedule', 'Time',
                value = as.POSIXct(settings$schedule),
                timepicker = TRUE),
            selectInput(
                'timezone', 'Timezone',
                choices = OlsonNames(),
                selected = settings$timezone),
            footer = tagList(actionButton('settings_update', 'Update'))
        ))
    })
    observeEvent(input$settings_update, {
        settings$title <- input$title
        settings$subtitle <- input$subtitle
        settings$schedule <- input$schedule
        settings$timezone <- input$timezone
        removeModal()
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

Future content to be uploaded later.

## Suggested Reading

* Hadley Wickham (2021): Mastering Shiny. https://mastering-shiny.org/
* Business Science (2020): The Shiny AWS Book. https://business-science.github.io/shiny-production-with-aws-book/

## Home Assignment

Create and deploy a Shiny application as described below. You can use any dataset you like. If you have no ideas on what data source(s) to use, check the below ideas. In any question, reach out on Slack or open a GitHub ticket.

Minimal project (for up to "B" grade):

* You need to create a Shiny application with `shinydashboard` layout using a sidebar menu
* Write at least one helper function and put into the `global.R` file to make it accessible for both `ui.R` and `server.R`
* Add at least 3 input elements using at least 2 different methods (eg dropdown, slider, checkbox)
* Add at least 3 different UI elements (eg text, table, map, UI, info and valueboxes)
* Use at least 2 menu items
* Use `reactive` values
* The change of the input elements have to change the UI elements showed to the user
* Deploy the app into shinyapps.io, upload the code into Moodle with a reference to your shinyapps.io application

Suggested project (for up to "B" grade):

* Implement the application described in the above steps
* Add at least 5 different UI elements, including one from htmlwidgets.org
* Deploy the app via shinyproxy (covered on the second day of the course)

Some ideas for data source:

* `nycflights13` package
* https://github.com/rfordatascience/tidytuesday/
* https://www.kaggle.com/datasets

Deadline: May 20, 2021

## Contact

File a [GitHub ticket](https://github.com/daroczig/CEU-DV3/issues).
