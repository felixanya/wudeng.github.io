



资源来源：
    Unity自动打包资源: Assets
    Resources: Assets/Resources
        Resources.Load动态加载，直接打包到游戏包中，无法增量更新
    AssetBundle
        与游戏包分离，通过www类来加载。分包发布。

方案：
    资源还是放在Resources下面，但是这些资源同时也会打包到AssetBundle中。
    代码中所有加载资源的地方都通过自己的ResouceManager来加载：由ResouceManager来决定是调用Resources.Load来加载资源还是从
    AssetBundle加载。
    
    resourcesinfo
        version
        bundle - hashcode
        
    资源
        BundleName
