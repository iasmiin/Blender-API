# Blender-API
## Iterating over objects in a collection:

```
# This script iterates over the objects in the "Data Collection" collection and renders animation for each object
# The animation consists of a camera "Camera" following a bezier circle "BezierCircle"
import bpy

# Get the collection
data_collection = bpy.data.collections.get("Data Collection")

# Get the camera and the bezier circle
camera = bpy.data.objects.get("Camera")
circle = bpy.data.objects.get("BezierCircle")

# Set up the render settings
bpy.context.scene.render.engine = 'CYCLES'
bpy.context.scene.render.resolution_x = 1920
bpy.context.scene.render.resolution_y = 1080
bpy.context.scene.render.resolution_percentage = 100
bpy.context.scene.render.image_settings.file_format = 'BMP'
bpy.context.scene.render.image_settings.color_mode = 'RGB'

# Iterate over the objects in the collection
for i, obj in enumerate(data_collection.objects):
    # Set the current object as visible and all other objects as hidden
    for other_obj in data_collection.objects:
        if other_obj == obj:
            other_obj.hide_render = False
        else:
            other_obj.hide_render = True
    
    # Set the camera to follow the bezier circle
    camera.constraints[0].target = circle
    camera.constraints[0].use_curve_follow = True
    
    # Render the animation using the bezier circle
    bpy.context.scene.frame_start = 1
    bpy.context.scene.frame_end = 10
    bpy.context.scene.render.filepath = "C:/.../render_output_{}.bmp".format(i)
    bpy.ops.render.render(animation=True)
    
    # Reset the camera's constraints
    camera.constraints[0].target = None
    camera.constraints[0].use_curve_follow = False
    
    # Hide all objects again for the next iteration
    for other_obj in data_collection.objects:
        other_obj.hide_render = False

    bpy.utils.unregister_class(TestPanel)
   
if __name__ == "__main__":
    register()
