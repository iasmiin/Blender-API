# Blender-API
## Importing models:

```
# This script loops through files in a specified directory and imports .stl files into the current Blender scene and moves them into a specified collection

import bpy
import os

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
   
    def draw(self, context):
        layout = self.layout
        scene = context.scene
        
        # Text input for directory path
        layout.label(text="Directory Path:")
        layout.prop(scene, "template_path", text="")
        
        # Button for importing templates
        layout.operator("import_templates.button", text="Import Templates")

class ImportTemplatesOperator(bpy.types.Operator):
    bl_idname = "import_templates.button"
    bl_label = "Import Templates"
    
    def execute(self, context):
        scene = context.scene
        template_path = scene.template_path
        
        # Check if directory exists
        if not os.path.exists(template_path):
            self.report({'ERROR'}, "Directory does not exist!")
            return {'CANCELLED'}
        
        # Get the target collection
        collection_name = "Collection 2"
        collection = bpy.data.collections.get(collection_name)
        if not collection:
            collection = bpy.data.collections.new(collection_name)
            bpy.context.scene.collection.children.link(collection)
        
        # Loop through files in directory and import .stl files
        for filename in os.listdir(template_path):
            filepath = os.path.join(template_path, filename)
            if filename.endswith(".stl") and os.path.isfile(filepath):
                bpy.ops.import_mesh.stl(filepath=filepath, directory=template_path)
                
                # Move imported object to collection
                obj = context.selected_objects[0]
                obj_name = obj.name
                if obj_name not in collection.objects:
                    collection.objects.link(obj)
                else:
                    print(f"Object '{obj_name}' already in collection '{collection_name}'")
        
        return {'FINISHED'}

def register():
    bpy.utils.register_class(TestPanel)
    bpy.utils.register_class(ImportTemplatesOperator)
    bpy.types.Scene.template_path = bpy.props.StringProperty(name="Template Path", default="")
   
def unregister():
    bpy.utils.unregister_class(TestPanel)
    bpy.utils.unregister_class(ImportTemplatesOperator)
    del bpy.types.Scene.template_path

if __name__ == "__main__":
    register()
