# Blender-API
## Implementing checkboxes:

```
import bpy
from bpy.props import BoolProperty

# Define the boolean properties
bpy.types.Scene.prop_00 = BoolProperty(name="Checkbox_0", default = False)
bpy.types.Scene.prop_01 = BoolProperty(name="Checkbox_1", default = False)
bpy.types.Scene.prop_02 = BoolProperty(name="Checkbox_2", default = False)

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    # Define the layout of the panel   
    def draw(self, context):
        layout = self.layout
        scene = context.scene
        box = layout.box()
        box.label(text= "Select checkbox", icon= 'FILE_FOLDER')
        box.prop(scene, "prop_00")
        box.prop(scene, "prop_01")
        box.prop(scene, "prop_02")

# Use bpy.context.scene.prop_00 to assign any condition to Checkbox_0.

# Register the Panel class
def register():
    bpy.utils.register_class(TestPanel)
   
def unregister():
    bpy.utils.unregister_class(TestPanel)

# Register the panel if this script is run as the main program
if __name__ == "__main__":
    register()
