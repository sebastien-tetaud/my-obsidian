[[Headless Rendering installation]]
[[Field Vector Quiver Plot]]



```bash
ffmpeg -framerate 30 -pattern_type glob -i '/home/ubuntu/project/destine-super-resolution/data/frame/*.png' -c:v libx264 -pix_fmt yuv420p video.mp4
```



```
ffmpeg -i era5_europe_t2m_wind.mp4 -r 10 -vf "scale=1080:-1" era5_europe_t2m_wind.gif
```