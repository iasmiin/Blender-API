# Blender-API
## Implementing a language selection feature:

```
import bpy

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'

    # Define the words in English and Portuguese
    words = {
        "Import Part": {"en": "Import Part", "pt": "Importar Peça"},
        "STL File": {"en": "STL File", "pt": "Arquivo .stl"},
        "Part Name": {"en": "Part Name", "pt": "Nome da Peça"},
        "Render": {"en": "Render", "pt": "Renderizar"},
        "Animation Render": {"en": "Animation Render", "pt": "Renderizar Animação"},
        "English": {"en": "English", "pt": "Inglês"},
        "Portuguese": {"en": "Portuguese", "pt": "Português"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene

        box = layout.box()
        box.label(text=self.words["Import Part"][bpy.context.window_manager.language], icon='IMPORT')
        box.operator("import_mesh.stl", text=self.words["STL File"][bpy.context.window_manager.language])

        layout.separator()
        
        row = layout.row()
        row.label(text=self.words["Part Name"][bpy.context.window_manager.language])
        row.prop(scene, "part_name", text="")

        layout.separator()

        box = layout.box()
        box.label(text=self.words["Render"][bpy.context.window_manager.language], icon='RENDER_STILL')
        props = box.operator("render.render", text=self.words["Animation Render"][bpy.context.window_manager.language])
        props.animation = True
        props.use_viewport = True

        # Add the language selection buttons
        row = layout.row()
        if bpy.context.window_manager.language == "en":
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "pt"
        else:
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language]).language = "en"

def register():
    bpy.utils.register_class(TestPanel)
    bpy.types.WindowManager.language = bpy.props.StringProperty(default="en")
    bpy.types.Scene.part_name = bpy.props.StringProperty(name="Part Name")

def unregister():
    bpy.utils.unregister_class(TestPanel)
    del bpy.types.WindowManager.language


if __name__ == "__main__":
    register()
