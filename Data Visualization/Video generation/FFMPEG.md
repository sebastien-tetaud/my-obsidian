[[Headless Rendering installation]]
[[Cheat Sheet]]
[[Field Vector Quiver Plot]]


```bash
ffmpeg -framerate 30 -pattern_type glob -i '/home/ubuntu/project/destine-super-resolution/data/frame/*.png' -c:v libx264 -pix_fmt yuv420p video.mp4
```