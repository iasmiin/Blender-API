# Blender-API
## Implementing a language selection feature:

### ↪ Language selection using dictionary: 
```
# The language selection in this code allows the user to switch between English and Portuguese translations of the text displayed in the user interface of the Blender add-on

import bpy
from bpy.types import Operator

# Define a custom panel class
class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "VIEW3D_PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    # The words in English and Portuguese are stored on a dictionary called 'words'
    # Each word that needs to be translated is a key in the dictionary, and the values associated with each key are dictionaries that contain the translations for that key in English and Portuguese
    words = {
        "Import Model": {"en": "Import Model", "pt": "Importar Modelo"},
        "STL File": {"en": "STL File", "pt": "Arquivo .stl"},
        "Model Name": {"en": "Model Name", "pt": "Nome do Modelo"},
        "English": {"en": "English", "pt": "Inglês"},
        "Portuguese": {"en": "Portuguese", "pt": "Português"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene

        box = layout.box()
        box.label(text=self.words["Import Model"][bpy.context.window_manager.language], icon='IMPORT')
        box.operator("import_mesh.stl", text=self.words["STL File"][bpy.context.window_manager.language])

        layout.separator()
        
        row = layout.row()
        row.label(text=self.words["Model Name"][bpy.context.window_manager.language])
        row.prop(scene, "model_name", text="")

        layout.separator()
        
        # Add the language selection buttons
        # The button checks the current language setting of the WindowManager object and displays the appropriate text for the opposite language
        # When the user clicks the button, it executes an operator wm.set_language that sets the WindowManager.language property to either "en" or "pt"
        row = layout.row()
        if bpy.context.window_manager.language == "en":
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "pt"
        else:
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language]).language = "en"

# Define a custom operator class for setting the UI language
class SetLanguageOperator(Operator):
    bl_idname = "wm.set_language"
    bl_label = "Set Language"
    language: bpy.props.StringProperty()
    
    # Set the language in the window manager
    def execute(self, context):
        bpy.context.window_manager.language = self.language
        return {'FINISHED'}

# Register the panel and the operator and set the custom properties 'language selection' and 'part name'
def register():
    bpy.utils.register_class(TestPanel)
    bpy.utils.register_class(SetLanguageOperator)
    bpy.types.WindowManager.language = bpy.props.StringProperty(default="en")
    bpy.types.Scene.model_name = bpy.props.StringProperty(name="Model Name")

def unregister():
    bpy.utils.unregister_class(TestPanel)
    bpy.utils.unregister_class(SetLanguageOperator)
    del bpy.types.WindowManager.language

if __name__ == "__main__":
    register()
```
### → Language selection using lists: 
```
import bpy

class TestPanel(bpy.types.Panel):
    bl_label = "Panel"
    bl_idname = "VIEW3D_PT_TestPanel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'

    ENGLISH_WORDS = [
        "Import Model", "STL File", "Model Name"
    ]
    PORTUGUESE_WORDS = [
        "Importar Modelo", "Arquivo .stl", "Nome do modelo:"
    ]

    def draw(self, context):
        layout = self.layout
        scene = context.scene
        obj = context.object

        # Language selection buttons
        row = layout.row()
        row.prop(scene, "language", expand=True)

        # Get the selected language
        language = scene.language

        # Set the appropriate words based on the selected language
        if language == 'ENGLISH':
            words = self.ENGLISH_WORDS
        else:
            words = self.PORTUGUESE_WORDS

        box = layout.box()
        box.label(text= words[0], icon= 'IMPORT')
        box.operator("import_mesh.stl", text=words[1])

        layout.separator()
        
        box = layout.row()
        box.label(text=words[2])
        box.prop(scene, "part_name", text="")

def register():
    bpy.utils.register_class(TestPanel)
    bpy.types.Scene.language = bpy.props.EnumProperty(
        items=[('ENGLISH', "English", ""), ('PORTUGUESE', "Portuguese", "")],
        name="Language", description="Select the language of the panel")
    bpy.types.Scene.part_name = bpy.props.StringProperty(name="Part Name")

def unregister():
    bpy.utils.unregister_class(TestPanel)
    del bpy.types.Scene.language

if __name__ == "__main__":
    register()

