# Blender Game Model Creation Workflow

# 1. <a href="#1">High poly model creation</a>
# 2. <a href="#2">High poly to low poly model creation</a>
# 3. <a href="#3">Low poly model UV map creation</a>
# 4. <a href="#4">High poly to low poly model normal map baking</a>
# 5. <a href="#5">Low Poly Model Shading (Materials/Texture Painting)</a>
# 6. <a href="#6">Low poly model diffuse to texture baking</a>
<br>

# <a href="999">General Blender Workflow</a>

<br>
<div id="1"></div>

# High poly model creation

**Create high poly model of whatever you want to make**

## Important

- ### Keep s cale of every object at 1,1,1
- ### Details everywhere, so it does not look strange/empty
- ### Use many simpler objects instead of less complicated objects
- ### Use snapping to excactly line up objects
- ### Use a Collection for every seperate object and assign names from 1 to X for objects and collections
- ### Use bevel mod workflow for smoothness of sharp edges and meshMACHINE Chamfer/Fillets for actual geometry
- ### Think about what the model needs to be logical to come up with model ideas (example a rifle needs a barrel, main body, grip, magazine, bullet chamber, etc.)
- ### Random features of model (example:_robot random amount of arms, etc.)
- ### Bolts, Ingrave, Outgrave, Handle, Light, Grills, etc.
- ### Look at other games and copy details/ideas
- ### Fix all topology problems, clamp all bevels, remove unnecessary vertices already

<br>
<div id="2"></div>

# High poly to low poly model creation

## Important

- ### Duplicate all high poly collections with meshes
- ### Remove/Hide bevel mod if you want to bake the bevels later
- ### Remove every vertex that does not contribute to the geometry or could be useful for material seperation
- ### Triangulate mesh at the end maybe with triangulate mod maybe not
- ### Use high poly details that define the silhouette, need shadows or are too big to bake to a normal map
- ### Use the right shading with custom normals on the low poly model aswell

<br>
<div id="3"></div>

# Low poly model UV map creation

## Important

- ### Placing seams
  - #### to prevent uv map streching
  - #### to seperate parts of objects
  - #### on edges between material change
  - #### on unvisible / less visible edges
- ### Select every object of model and go to edit mode
- ### Work on part by part not every object at the same time, but after you went to edit mode with all objects
- ### Use seam at every sharp/beveled edge
- ### Increase scale of islands that have high detailed geometry
<br>
<div id="4"></div>

# High poly to low poly model normal map baking

## Important

- ### Check face orientation of HP model
- ### Bake normal maps with 1 Samples, otherwise its time waste
- ### Activate selected to active and use extrusion cage or cage object
- ### Check for normals outside, if everything but the baking objects are hidden in rendered mode, edges have to be sharped right in the hp model
- ### Select high poly model and then low poly model and bake using the cage to a texture in the material of the low poly model
- ### Normal map texture settings: Source: Generated, Enable Float Buffer, NON COLOR
- ### Use normal map node between normal map texture and normal map shader node
- ### Use strong bevels, so normal maps can represent the bevels better
- ### Dont use 90° or other steep angles, as normal maps cant represent them
- ### When baking multiple materials into one texture, the texture has to be in every material and it has to be selected in every material before the start of baking
- ### Island margin = texture res / 128
- ### Mark all beveled edges as sharp
- ### Use seam at every sharp/beveled edge
- ### Use Cage extrusion or a Cage object
- ### Use Subsurf mod on low poly and set mean crease on every edge to 1, so you have a high poly model with the same shape to get a better baking result
- ### A Displacement mod after the sub mod can also help with the distortion problem

<br>
<div id="5"></div>

# Low Poly Model Shading (Materials/Texture Painting)

## Important

- ### Bake from PBR Material to texture in Diffuse material to check the result
- ### Use HDRI and set metal roughness, so that its as low as possible without being able to see details from the hdri

<br>
<div id="6"></div>

# Low poly model diffuse to texture baking

## Important

- ### Use HDRI for baking

<br>
<div id="999"></div>


# General Blender Workflow

## Shading / Smoothness

- ### Use Shade Smooth + Auto Smooth + Custom Normals
- ### Mark sharp edges
- ### Use bevel modifier for sharp edges and use bevel weight for complete control

<br> 

## Other

- ### Use 3-24 edge topology only in game model creation
