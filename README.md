# Blender-API
## Implementing a dropdown menu:

```
# This code defines a custom panel for Blender with a dropdown menu to import files in different formats.

import bpy

# The TestPanel class defines the properties of the panel
class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
   
   # The panel is drawn in the Blender UI
   # The row.menu() method adds a menu to the row with the ID name "IMPORT_MT_filter_menu" and the label "Import" and an icon
    def draw(self, context):
        layout = self.layout
        row = layout.row()
        row.menu("IMPORT_MT_filter_menu", text="Import", icon='DOWNARROW_HLT')

# The IMPORT_MT_filter_menu class defines the properties of the dropdown menu
class IMPORT_MT_filter_menu(bpy.types.Menu):
    bl_label = "Import"
    bl_idname = "IMPORT_MT_filter_menu"
    
    # 'draw()' creates a layout and adds three operators to it
    def draw(self, context):
        layout = self.layout
        layout.operator("import_mesh.stl", text="STL File")
        layout.operator("import_scene.x3d", text="X3D File")
        layout.operator("import_mesh.ply", text="PLY File")

# Class registration
def register():
    bpy.utils.register_class(TestPanel)
    bpy.utils.register_class(IMPORT_MT_filter_menu)
   
def unregister():
    bpy.utils.unregister_class(TestPanel)
    bpy.utils.unregister_class(IMPORT_MT_filter_menu)
    
if __name__ == "__main__":
    register()
