# Point Cloud 

pcloud -> acceleration data structure (use kdtree nearest neighbours) (bvh) (octree, kdtree)

pcopen -> kdtree
pcfind -> bvh

most commom, pcopen, pcfind

pcopen -> query- > handle 

pcfind -> array pt indises



```
float radius = ch("r");
int maxpts = chi("maxpts");
int handle = pcopen(0,"P", @P, radius, maxpts);

@density = pcnumfound(handle) / float (maxpts);
