# MadBombus

Urban bumble bee foraging project

## Plot geographically weighted regression results as interactive maps

```{r}

TrGWGLMread.csv(TrGWGLM, "TrGWGLM.csv")

TrGWGLM2<- TrGWGLM  %>% 
  mutate(ZoningType = factor(
      Zoning.x,
      levels = c("C", "R", "A", "P"),
      labels = c("Commercial", "Residential", "Agricultural", "Park"))) %>% 
      rename(Long = lng, 
      Lat = lat) 
    

pal <- colorNumeric(
  palette = "plasma",
  domain = TrGWGLM2$Est.Cover)


SigCover<-TrGWGLM2 %>% filter(P_Cover < 0.05) 
SigDensity<-TrGWGLM2 %>% filter(P_Density < 0.05) 
SigSp<-TrGWGLM2 %>% filter(P_Sp < 0.05) 
SigPLSp<-TrGWGLM2 %>% filter(P_PLspR < 0.05) 

CoverEst_map<-leaflet(TrGWGLM2) %>% 
  #Base groups
  addProviderTiles(providers$Stamen.Toner, group = "Toner") %>%
  addProviderTiles(providers$Esri.WorldImagery, group = "Imagery") %>%
  #Overlays
  addCircleMarkers(radius = 7,
                           stroke = TRUE,
                           color = "black",
                           weight = 1,
                           fillColor = ~pal(as.numeric(TrGWGLM2$Est.Cover)),
                           fillOpacity = 0.5,
                           opacity = 0.5,
                   label = ~paste("Cover effect estimate: ", Est.Cover),
                   group = "All Effects") %>%
  
addCircleMarkers(data = SigCover,
                 radius = 7,
                           stroke = TRUE,
                           color = "black",
                           weight = 1,
                           fillColor = ~pal(as.numeric(TrGWGLM2$Est.Cover)),
                           fillOpacity = 1,
                           opacity = 1,
                   label = ~paste("Cover effect estimate: ", Est.Cover),
                 group = "Significant") %>%
  #Add legend
  addLegend(
    "bottomright",
    pal = pal,
    values = ~Est.Cover,
    title = "Flower Cover Effect",
    opacity = 1)  %>%
  
# Layers control
  addLayersControl(
    baseGroups = c( "Toner", "Imagery"),
    overlayGroups = c("All Effects", "Significant"),
    options = layersControlOptions(collapsed = FALSE)
  )


saveWidget(CoverEst_map, file = "CoverEst_map.html")

```

