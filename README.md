# Blender-API
## Implementing a dropdown menu:

```
import bpy

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
   
    def draw(self, context):
        layout = self.layout
        row = layout.row()
        row.menu("IMPORT_MT_filter_menu", text="Import", icon='DOWNARROW_HLT')

class IMPORT_MT_filter_menu(bpy.types.Menu):
    bl_label = "Import"
    bl_idname = "IMPORT_MT_filter_menu"

    def draw(self, context):
        layout = self.layout
        layout.operator("import_mesh.stl", text="STL File")
        layout.operator("import_scene.x3d", text="X3D File")
        layout.operator("import_mesh.ply", text="PLY File")

def register():
    bpy.utils.register_class(TestPanel)
    bpy.utils.register_class(IMPORT_MT_filter_menu)
   
def unregister():
    bpy.utils.unregister_class(TestPanel)
    bpy.utils.unregister_class(IMPORT_MT_filter_menu)
    
if __name__ == "__main__":
    register()
