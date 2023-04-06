# Blender-API
## Implementing a language selection feature:

```
# The language selection in this code allows the user to switch between English and Portuguese translations of the text displayed in the user interface of the Blender add-on
import bpy

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'

    # The words in English and Portuguese are stored on a dictionary called 'words'
    # Each word that needs to be translated is a key in the dictionary, and the values associated with each key are dictionaries that contain the translations for that key in English and Portuguese
    words = {
        "Import Part": {"en": "Import Part", "pt": "Importar Peça"},
        "STL File": {"en": "STL File", "pt": "Arquivo .stl"},
        "Part Name": {"en": "Part Name", "pt": "Nome da Peça"},
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

        # Add the language selection buttons
        # The button checks the current language setting of the WindowManager object and displays the appropriate text for the opposite language
        # When the user clicks the button, it executes an operator wm.set_language that sets the WindowManager.language property to either "en" or "pt"
        row = layout.row()
        if bpy.context.window_manager.language == "en":
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "pt"
        else:
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language]).language = "en"

# Register the panel and set the custom properties 'language selection' and 'part name'
def register():
    bpy.utils.register_class(TestPanel)
    bpy.types.WindowManager.language = bpy.props.StringProperty(default="en")
    bpy.types.Scene.part_name = bpy.props.StringProperty(name="Part Name")

def unregister():
    bpy.utils.unregister_class(TestPanel)
    del bpy.types.WindowManager.language


if __name__ == "__main__":
    register()
