# Extraccion-de-valores-media-areal-de-una-cuenca
Etrae los valores de precipitacion de una cuenca desde un NETCDF
setwd("D:/01 DAVID/01 UNAP-FIA/02 DECIMO SEMESTRE/LEGISLACION Y GESTION HIDRICA/PGRH CUENCA ILAVE")
library(raster)
library(ncdf4)
library(sf)
library(sp)
library(latticeExtra)

pisco_pm = brick("data.nc", varnames = "Prec")
nlayers(pisco_pm)
spplot(pisco_pm[[1]])
crs(pisco_pm) <- CRS("+proj=longlat +datum=WGS84 +no_defs")
Cuenca_Ilave <- st_read("Cueca_Ilave_PROJECT.shp")
plot(Cuenca_Ilave[1], axes = T, asp=1, col = "Cyan", main = "Cuenca Ilave")
print(Cuenca_Ilave)

CuencaIlaveT <- st_transform(Cuenca_Ilave, crs(pisco_pm))

Pm_C_Ilave <- mask(pisco_pm, CuencaIlaveT)
valor_medio <- cellStats(Pm_C_Ilave, mean, na.rm = TRUE)
fecha <- seq(as.Date("1981/1/1"), as.Date("2016/12/31"), by = "month")

Pmm_CuencaIlave <- data.frame(Fecha = fecha, valor_medio = valor_medio)
print(Pmm_CuencaIlave)
plot(Pmm_CuencaIlave, type = "l")

write.csv(Pmm_CuencaIlave, "Precipitacion_media_Areal_ilave_2024.csv", row.names = FALSE)
