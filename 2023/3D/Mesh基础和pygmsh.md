# pygmsh
在Gmsh中，用户必须为创建的每个点、线、面、体提供唯一的ID。当创建了大量实体(ID)时，就会显得很混乱，因为不知道哪些ID在使用。

# geometry回顾
Gmsh的geometry模块提供了简单的CAD引擎，它使用的是边界表达法。