

[[Field Vector Quiver Plot]] [[FFMPEG]]
## Colorbar align with pcolormesh 


```python
import cartopy.feature as cfeature
import cartopy.crs as ccrs
import matplotlib.pyplot as plt

  
fig, ax = plt.subplots(figsize=(19.2, 10.8), dpi=100, subplot_kw={'projection': ccrs.PlateCarree()})
heatmap = ax.pcolormesh(dsm['lon'], dsm['lat'],dsm.values,cmap="terrain", transform=ccrs.PlateCarree())

ax.add_feature(cfeature.LAND)
pos = ax.get_position()
cbar_ax = fig.add_axes([pos.x1 + 0.01, pos.y0, 0.02, pos.height])
cbar = fig.colorbar(heatmap, cax=cbar_ax, orientation='vertical')
cbar.set_label("[m]")
ax.set_title('DEM')
plt.show()
```

### Save Figure without bordel

- Don't put any title or xlabel/ylabel and colorbar()

```python


import cartopy.feature as cfeature
import cartopy.crs as ccrs
import matplotlib.pyplot as plt


fig, ax = plt.subplots(figsize=(19.2, 10.8), dpi=100, subplot_kw={'projection': ccrs.PlateCarree()})
heatmap = ax.pcolormesh(dsm['lon'], dsm['lat'], dsm.values, cmap="terrain", transform=ccrs.PlateCarree())
plt.show()

fig.savefig("figure.png", pad_inches=0, bbox_inches='tight', transparent=True)

plt.close()
```