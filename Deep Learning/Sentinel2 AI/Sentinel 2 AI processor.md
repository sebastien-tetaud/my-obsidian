```
https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Collection/Name eq 'SENTINEL-2' and OData.CSC.Intersects(area=geography'SRID=4326;POLYGON((3.2833 45.3833, 11.2 45.3833, 11.2 50.1833, 3.2833 50.1833, 3.2833 45.3833))') and (contains(Name,'MSIL1C') or contains(Name,'MSIL2A')) and ContentDate/Start gt 2025-01-01T00:00:00.000Z and ContentDate/Start lt 2025-01-15T23:59:59.999Z and Attributes/OData.CSC.DoubleAttribute/any(att:att/Name eq 'cloudCover' and att/OData.CSC.DoubleAttribute/Value le 100)&$orderby=ContentDate/Start&$top=100&$count=true

```

V0 

bbox = [3.2833, 45.3833, 11.2, 50.1833]
start_date = datetime(2024, 1, 1)
end_date = datetime(2025, 1, 1)
cloud_cover = 100 


V1 
bbox = [3.2833, 45.3833, 11.2, 50.1833]
start_date = datetime(2020, 1, 1)
end_date = datetime(2025, 1, 1)
max_items = 1000
max_cloud_cover = 10