# Blender-API
# Implementing checkboxes:

```
import bpy
from bpy.props import BoolProperty

bpy.types.Scene.prop_00 = BoolProperty(name="Arruela", default = False)
bpy.types.Scene.prop_01 = BoolProperty(name="Parafuso", default = False)
bpy.types.Scene.prop_02 = BoolProperty(name="Porca", default = False)

class TestPanel(bpy.types.Panel):
    bl_label = "Data Augmentation"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Data Augmentation'
   
    def draw(self, context):
        layout = self.layout
        scene = context.scene
        box = layout.box()
        box.label(text= "Selecionar Classe", icon= 'FILE_FOLDER')
        box.prop(scene, "prop_00")
        box.prop(scene, "prop_01")
        box.prop(scene, "prop_02")

#Teste simples com as checkboxes
if bpy.context.scene.prop_00 == True:
    bpy.context.scene.render.filepath = 'C:/Users/iasmi/Downloads/b/image_####_test.png'
else:
    bpy.context.scene.render.filepath = 'C:/Users/iasmi/Downloads/a/image_####_test.png'       

def register():
    bpy.utils.register_class(TestPanel)
   
def unregister():
    bpy.utils.unregister_class(TestPanel)
   
if __name__ == "__main__":
    register()
