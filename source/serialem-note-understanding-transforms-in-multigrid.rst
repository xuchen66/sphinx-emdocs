SerialEM Note: Understanding Transforms in Multigrid Operation

Abstract

The current design of Multigrid in SerialEM is not fully automated yet, but it follows a structured three-step process: LMM (Low-Magnification Mapping), MMM (Medium-Magnification Mapping), and final data acquisition. Each grid is reloaded at least twice during this process.

Typically, LMM maps are collected for all grids in a single automated run. The system loads each grid and acquires its LMM map. At this stage, "good" mesh areas are selected using Navigator point items or polygons on each grid’s LMM map.

Following that, MMM maps are collected—again, automatically—for all grids. Holes are then defined on each MMM map, and the program is instructed to collect all the corresponding images automatically.

While this workflow sounds straightforward, complications arise due to physical changes that occur when grids are reloaded. Reloading a grid inevitably introduces some degree of rotation and translation, which means previously defined Navigator items may no longer align correctly. It's critical to ensure these items remain usable without having to acquire new maps. This is a key aspect of maintaining efficiency and consistency in the workflow.

Things become even more complex when using multi-hole acquisition with Multigrid. Each grid may differ significantly in geometry, requiring accurate Image Shift vectors to be determined automatically for each grid—and sometimes even for each map—without manual intervention.

How is all of this achieved? In this note, I want to share my understanding of the process, so that new users can better grasp what's happening behind the scenes and avoid treating it as a black box.
