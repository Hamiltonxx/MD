# GUI
## Asset Browser
There are different types of assets within Blender: materials, objects, textures, etc. we can import them directly.
## Workspaces
The primary workspaces are Layout, Modeling, Sculpting, UV Editing, Texture Paint, Shading, Animation, Rendering, Compositing and Scripting.
## Toolbar
The toolbar, on the left of the Blender interface, is divided into four parts in Object mode.  
1. selection tools  
2. transformations: Move,Rotate,Scale and Transform  
3. Annotate and Measure  
4. add some primitives interactively in the 3D Viewport  
Two main ways to model in Blender: Object model and Edit model.
## Eevee
the new realtime rendering engine.  
Eevee is a lot faster than Cycles.  
Rendering transparent objects with realtime engines like Eevee does not give the same photorealistic rendering quality as Cycles.
## USD Importer
USD = Universal Scene Description, is used to create complex scenes collaboratively in a 3D world.  
Nvidia, starting from this file format, has built Omniverse.  
This format is significant and could be the future of 3D modeling production.
## Default Interface
3D Viewport, the central workspace, is present with three objects: a Cube, a Camera, a Light. We can begin creating objects in Blender and start working immediately with these three elements.
## Object Interaction Mode
1. Object mode, we work on the thing as a whole, we modify the entity's position, rotation, and dimension.  
2. Edit mode, for suboject editing. vertices,edges and faces for meshes and control points for curves, surfaces.  
3. Sculpt mode, mesh-only mode for creating organic shapes in realtime and realistic sculptural modeling with virtual clay.  
4. Vertex Paint mode, mesh-only mode  
5. Weight Paint mode, mesh-only mode  
6. Texture Paint mode, mesh-only mode
## Editors and Views

# Modeling Inorganic and Organic Objects in Blender
Blender's virtual space uses a Cartesian reference system and the Euclidean space.  
Objects,materials and lights compose a digital space.
## Curve
Bezier curves are mainly suitable for creating tubes,wires,bottles,glasses,texts,logos,etc.  
Nonuniform rational B-splines(NURBS) are sutiable for organic and beveled shapes,vehicles,and industrial design objects.  
The subobject of the curve is the spline,which is composed of control points. Three types of splines: Poly,Bezier,NURBS
## Surface
Surfaces are the extension of curves in the three-dimensional space.
## Metaball
Metaballs are strange and simple objects defined by pure mathematical expressions directly inside Blender.
## Text
The Text object is composed of mathematically calculated vector data.
## Empty and Image
Empty is a point in space without geometry. Empty types: Plain Axes, Arrows, Single Arrow, Circle, Cube, Sphere, Cone, Image.  
When create an image, an empty frame appears in the 3D view displayed in wireframe.
## Light
Four types of lights: point, sun, spot, area.
## Camera
Cameras are used to take photographs or make movies in virtual spaces.
## Mesh Modeling
Three modeling modes: Object, Edit, and Sculpt.  
Object mode allows us to perform basic operations on entities. We make most mesh editing operations in Edit mode by vertices, edges and faces. Sculpt mode allows us sculpting mesh with brushes.
## Object Model
### Transform
### Set Origin
The origin is the orange point that Blender by default places in the object's geometric center;it determines its location in the 3D Viewport.
### Mirror
### Clear and Apply
Initially the location and rotation values are 0 while the scale is 1. If we move,rotate,or scale the object,the values change with the modifications made. We can reset the transformations. 
### Snap
### Duplicate
### Link/Transfer Data
### Join
The objects to be joined must be of the same type.
### Convert
We can transform a mesh into a curve and vice versa.
### Show/Hide
### Delete
## Edit Mode
### Mesh Visualization
We must orient the face's normals in the right way to correctly visualize an object.  
Normals are the lines perpendicular to the faces.
### The Toolbar in Edit Mode
Annotate is a tool to write and draw freehand in 3D view.  
The Measure tool is also essential in geometry creation,especially in the mechanical and architectural industries. Instead,if we click and expand Overlays in the 3D View header in Edit mode, we will find particular measurement settings such as Edge Length,Edge Angle,Face Area, and Face Angle.  
Add Mesh allows us to add Cube,Cone,Cylinder,UVSphere,and IcoSphere objects by clicking and dragging directly on the 3D View,snapping on the grid or the object's surface.  
Extrude tool: to extrude vertices,edges,and faces,creating new geometry.  
Bevel: allows us to create new geometry by inserting new loops in the selected elements by chamfering and rounding edges and corners.  
Poly Build: create meshes extruding with just the cursor's movement.  

## Sculpting
In digital modeling,we use virtual chisels to edit shapes like working with real instruments.





