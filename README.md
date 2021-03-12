#
# This is a Shiny web application. You can run the application by clicking
# the 'Run App' button above.
#
# Find out more about building applications with Shiny here:
#
#    http://shiny.rstudio.com/
#
Link to github repo: https://github.com/Damon980/CensusApp

library(shiny)
library(maps)
library(mapproj)
source("helpers.R")
counties <- readRDS("data/counties.rds")

# User interface ----
ui <- fluidPage(
    titlePanel("censusVis"),
    
    sidebarLayout(
        sidebarPanel(
            helpText("Create demographic maps with 
        information from the 2010 US Census."),
            
            selectInput("var", 
                        label = "Choose a variable to display",
                        choices = c("Percent White", "Percent Black",
                                    "Percent Hispanic", "Percent Asian"),
                        selected = "Percent White"),
            
            sliderInput("range", 
                        label = "Range of interest:",
                        min = 0, max = 100, value = c(0, 100))
        ),
        
        mainPanel(plotOutput("map"))
    )
)

# Server logic ----
server <- function(input, output) {
    output$map <- renderPlot({
        data <- switch(input$var, 
                       "Percent White" = counties$white,
                       "Percent Black" = counties$black,
                       "Percent Hispanic" = counties$hispanic,
                       "Percent Asian" = counties$asian)
        
        color <- switch(input$var, 
                        "Percent White" = "red",
                        "Percent Black" = "blue",
                        "Percent Hispanic" = "green",
                        "Percent Asian" = "purple")
        
        legend <- switch(input$var, 
                         "Percent White" = "Percent White",
                         "Percent Black" = "Percent Black",
                         "Percent Hispanic" = "Percent Hispanic",
                         "Percent Asian" = "Percent Asian")
        
        percent_map(data, color, legend, input$range[1], input$range[2])
    })
}

# Run app ----
shinyApp(ui, server)
