问题：

客户端采用nvamesh寻路，服务器也需要寻路，怎么解决。

平面点集P德劳内三角化，最大化此三角剖分中三角形的最小角。换句话说，此算法尽量避免出现极瘦的三角形。
    DT(P)


方案1：
    客户端导出点，三角形。
    服务器快速判断点是否在三角形内。
        三角形区域，建立四个排序列表
            xmin,xmax, ymin, ymax
        给点x, 快速找到区间对应的三角形列表。
        给点y，快速找到区间对应的三角形。
        求交集，判断是否在三角形内。



github.com/xtaci/navmesh/demo
    add 64 bit gcc.exe to path
    go run main.go
    
    输入：mesh.json，点列表，三角形列表，遍历创建邻接表，计算相邻三角形中心的距离
    给定起点和终点，遍历三角形列表，找出对应的三角形id。
    用Dijkstra计算起点到终点的最短路径即需要经过的三角形id列表。
    用navmesh计算路径。
        计算临边
    


NavMesh.Triangulate(out Vector3[] veticles, out Int[] indices)


从NavMesh导出grid的方法：
    NavMesh.CalculateTriangulation计算得出顶点和边。
        vertices
        layers
        indices
    根据vertices计算得出x和z的最大最小值，以及y的最大值，取整。
    对于每一层：
    

http://www.luzexi.com/unity3d/%E5%89%8D%E7%AB%AF%E6%8A%80%E6%9C%AF/2013/10/06/Unity3D%E6%9E%B6%E6%9E%84%E8%AE%BE%E8%AE%A1NavMesh%E5%AF%BB%E8%B7%AF.html
http://liweizhaolili.lofter.com/post/1cc70144_86a939e 
    
    

    
## recast & detour
开源游戏寻路引擎，被Unity，UE4等游戏引擎采用。包含两部分：Recast和Detour。
recast通过将输入的场景模型体素化生成相应的寻路网格，包含三个步骤：
- 体素化模型
- 分割联通区域
- 将区域简化为多边形

Detour通过使用Recast生成的Navmesh进行寻路。

Recast: navigation mesh construction toolset for games
    level geometry -> voxel mold -> mesh
        - building the voxel mold
        - partitioning the mold into simple regions
        - peeling off the regions as simple polygons
        - navmesh + detail mesh
        
    open source navmesh and navigation system in c++
    
Detour: path-finding and spatial reasoning toolkit


http://www.critterai.org/

http://www.cnblogs.com/yaukey/p/navmesh_data_export.html

http://www.voidcn.com/article/p-njcbdmxp-eo.html Navmesh -> bitmap


Mikko Mononen在Recast & Detour中生成NavMesh的基本流程收到GDC2006上演讲的启发：
[Crowds In A Polygon Soup: Next-Gen Path Planning](https://www.gdcvault.com/play/1013192/Crowds-In-A-Polygon-Soup), by David Miles
http://slideplayer.com/slide/3602002/

中文介绍：
https://wo1fsea.github.io/2016/08/21/A_Quick_Introduction_to_NavMesh/
    
    http://www.cnblogs.com/tomren/p/6283295.html
    
    代码：https://github.com/recastnavigation/recastnavigation
    文档：http://www.stevefsp.org/projects/rcndoc/prod/index.html
    
    fork：
    代码：https://github.com/masagroup/recastdetour
    文档：http://masagroup.github.io/recastdetour/index.html
    
    Overhead Obstacles
    changing world dynamic Obstacles
    
    boolean subtract
        find Obstacles-area intersections
        find area vert outside Obstacles
        loop CCW, extracting area: counterclockwise
    
    Dynamic Obstacles Summary
        find areas affected by dynamic Obstacles
        record any edges entering from outside
        boolean subtract
        partition any non-convex dynamic areas
        match up unconnected edges
        
    Fluid AI Navigation
        what can i do with my convex area graph
            very fast navigation ray-cast
            distance to edge queries
            repulsive fields on edges
            next corner determination
        
        
    Conclusions:
        Automated build of convex area graph
            efficiently represents the walkable free space
            operates on polygon soup mesh
            settable enemy size and shape
        Dynamic Obstacles update graph
            no speed overhead once updated
        Fluid AI navigation using the graph
            many useful queries performed rapidly
