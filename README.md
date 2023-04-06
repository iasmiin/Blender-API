# Blender-API
## Implementing a language selection feature:

```
import bpy

class TestPanel(bpy.types.Panel):
    bl_label = "Data Augmentation"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Data Augmentation'

    # Define the words in English and Portuguese
    words = {
        "Import Part": {"en": "Import Part", "pt": "Importar Peça"},
        "STL File": {"en": "STL File", "pt": "Arquivo .stl"},
        "WRL File": {"en": "WRL File", "pt": "Arquivo .wrl"},
        "PLY File": {"en": "PLY File", "pt": "Arquivo .ply"},
        "Apply Transformation": {"en": "Apply Transformation", "pt": "Aplicar Transformação"},
        "Render": {"en": "Render", "pt": "Renderizar"},
        "Animation Render": {"en": "Animation Render", "pt": "Renderizar Animação"},
        "English": {"en": "English", "pt": "Inglês"},
        "Portuguese": {"en": "Portuguese", "pt": "Português"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene
        obj = context.object

        box = layout.box()
        box.label(text=self.words["Import Part"][bpy.context.window_manager.language], icon='IMPORT')
        box.operator("import_mesh.stl", text=self.words["STL File"][bpy.context.window_manager.language])
        box.operator("import_scene.x3d", text=self.words["WRL File"][bpy.context.window_manager.language])
        box.operator("import_mesh.ply", text=self.words["PLY File"][bpy.context.window_manager.language])

        layout.separator()

        box = layout.box()
        box.label(text=self.words["Apply Transformation"][bpy.context.window_manager.language], icon='MOD_LINEART')

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
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language])
        else:
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "en"
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language])


def register():
    bpy.utils.register_class(TestPanel)
    bpy.types.WindowManager.language = bpy.props.StringProperty(default="en")

def unregister():
    bpy.utils.unregister_class(TestPanel)
    del bpy.types.WindowManager.language


if __name__ == "__main__":
    register()
