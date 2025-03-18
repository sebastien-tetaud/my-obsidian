
[[Ramses]]

An **octree** is a tree data structure where each internal node has eight children. Octrees are commonly used for spatial partitioning of 3D point clouds. Non-empty leaf nodes of an octree contain one or more points that fall within the same spatial subdivision. Octrees are a useful description of 3D space and can be used to quickly find nearby points. Open3D has the geometry type `Octree` that can be used to create, search, and traverse octrees with a user-specified maximum tree depth, `max_depth`.

https://www.open3d.org/docs/latest/tutorial/geometry/octree.html