# Blender-API
## Creating a collection

```
import bpy

# creates a new collection named "DEMO" using the bpy.data.collections.new() method
collection = bpy.data.collections.new('DEMO')

# renames the collection to "Data Collection"
collection.name = 'Data Collection'

# links the new collection to the master collection using the bpy.context.scene.collection.children.link()
# (the new collection will be visible in the Outliner and can be used to organize objects in the scene)
bpy.context.scene.collection.children.link(collection)
```

This script moves all objects from one collection to another:
```
# For two existing collections called Collection_1 and Collection_2:
coll_from = bpy.data.collections['Collection_1'] 
coll_to = bpy.data.collections['Collection_2']

# For each object in coll_from, the script links it to coll_to using coll_to.objects.link(ob). 
# After the loop finishes executing, all objects that were in coll_from are now in coll_to.
for ob in coll_from.objects:
    coll_to.objects.link(ob)
```

