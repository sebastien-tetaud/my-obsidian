
### Useful Links

https://www.ita.uni-heidelberg.de/~dullemond/software/radmc-3d/manual_radmc3d/installation.html

## Oct-tree Adaptive Mesh Refinement

An oct-tree refinened grid is called ‘grid style 1’ in RADMC-3D. It can be used in Cartesian coordinates as well as in spherical coordinates (Section [Coordinate systems](https://www.ita.uni-heidelberg.de/~dullemond/software/radmc-3d/manual_radmc3d/basicstructure.html#sec-coord-systems)).

You start from a normal regular base grid (see Section [Regular grids](https://www.ita.uni-heidelberg.de/~dullemond/software/radmc-3d/manual_radmc3d/gridding.html#sec-regular-grid)), possibly even with ‘separable refinement’ (see Section [Separable grid refinement in spherical coordinates (important!)](https://www.ita.uni-heidelberg.de/~dullemond/software/radmc-3d/manual_radmc3d/gridding.html#sec-separable-refinement)). You can then split some of the cells into 2x2x2 subcells (or more precisely: in 1-D 2 subcells, in 2-D 2x2 subcells and in 3-D 2x2x2 subcells). If necessary, each of these 2x2x2 subcells can also be split into further subcells. This can be repeated as many times as you wish until the desired grid refinement level is reached. Each refinement step refines the grid by a factor of 2 in linear dimension, which means in 3-D a factor of 8 in volume. In this way you get, for each refined cell of the base grid, a tree of refinement. The base grid can have any size, as long as the number of cells in each direction is an even number. For instance, you can have a 6x4 base grid in 2-D, and refine cell (1,2) by one level, so that this cell splits into 2x2 subcells.
Each refinement step refines the grid by a factor of 2 in linear dimension, which means in 3-D a factor of 8 in.

The order in which the base grid is scanned in this way is from `1` to `nx` in the innermost loop, from `1` to `ny` in the middle loop and from `1` to `nz` in the outermost loop.
 
- For each z-value (from 1 to `nz`):
	- For each y-value (from 1 to `ny`):
	    - For each x-value (from 1 to `nx`):
	        - Process the cell at coordinates (x, y, z)

y
|.__ x  . z 

## From AMR to Array

The size of your 3D array depends primarily on two factors: the base grid dimensions and the maximum refinement level. The number of branches (`nbranchmax`) doesn't directly affect the array size, but rather tells you how much memory you'll need to store the octree structure.