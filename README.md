import bpy
import os
from bpy.types import Operator
import math
import time

# Define a custom panel class
class TestPanel_1(bpy.types.Panel):
    bl_label = "Selecionar Idioma"
    bl_idname = "VIEW3D_PT_TestPanel_1"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'

    words = { 
        "English": {"en": "English", "pt": "Inglês"},
        "Portuguese": {"en": "Portuguese", "pt": "Português"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene

        row = layout.row()        
        if bpy.context.window_manager.language == "en":
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "pt"
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language]).language = "en"
        else:
            row.operator("wm.set_language", text=self.words["Portuguese"][bpy.context.window_manager.language]).language = "pt"
            row.operator("wm.set_language", text=self.words["English"][bpy.context.window_manager.language]).language = "en"

# Define a custom operator class for setting the UI language
class SetLanguageOperator(Operator):
    bl_idname = "wm.set_language"
    bl_label = "Set Language"
    language: bpy.props.StringProperty(name="Language")
    
    # Set the language in the window manager
    def execute(self, context):
        for panel_class in [TestPanel_1, TestPanel_2, TestPanel_3, TestPanel_4, TestPanel_5, TestPanel_6]:
            bpy.context.window_manager.language = self.language
        return {'FINISHED'}

###################################################################

class TestPanel_2(bpy.types.Panel):
    bl_label = "Criar Coleção"
    bl_idname = "VIEW3D_PT_TestPanel_2"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    words = {
        "Enter collection name": {"en": "Enter collection name:", "pt": "Insira nome da coleção:"},
        "Add Collection Name": {"en": "Add Collection Name:", "pt": "Adicionar Nome da Coleção"},
        "Enter render output path": {"en": "Enter render output path:", "pt": "Insira o caminho de saída da renderização:"},
        "Add Render Output Path": {"en": "Add Render Output Path", "pt": "Adicionar o Caminho de Saída da Renderização"},
        "Create Collection": {"en": "Create Collection", "pt": "Criar Coleção"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene
        
        row = layout.row()
        row.label(text=self.words["Enter collection name"][bpy.context.window_manager.language])
        row = layout.row()
        row.prop(context.scene, "coll_name", text="")
        row = layout.row()
        row.operator("render.add_collection_name", text=self.words["Add Collection Name"][bpy.context.window_manager.language])
        row = layout.row()
        row.label(text=self.words["Enter render output path"][bpy.context.window_manager.language])
        row = layout.row()
        row.prop(context.scene, "output_path", text="")
        row = layout.row()
        row.operator("render.add_output_path", text=self.words["Add Render Output Path"][bpy.context.window_manager.language])

        # Button for creating collection
        layout.operator("create_collection.button", text=self.words["Create Collection"][bpy.context.window_manager.language])


class RENDER_OT_add_collection_name(bpy.types.Operator):
    bl_idname = "render.add_collection_name"
    bl_label = "Add Collection Name"

    def execute(self, context):
        coll_name = context.scene.coll_name
        if coll_name not in TestPanel_6.names_list:
            TestPanel_6.names_list.append(coll_name)
        context.scene.coll_name = coll_name
        
        Create_Collection_Operator.names_list.append(coll_name)
        
        context.scene.coll_name = ""
        
        return {'FINISHED'}


class RENDER_OT_add_output_path(bpy.types.Operator):
    bl_idname = "render.add_output_path"
    bl_label = "Add Output Path"

    def execute(self, context):
        output_path = context.scene.output_path
        if output_path not in TestPanel_6.output_list:
            TestPanel_6.output_list.append(output_path)
        context.scene.output_path = output_path
        
        Create_Collection_Operator.output_list.append(output_path)
        
        context.scene.output_path = ""
        
        return {'FINISHED'}


class Create_Collection_Operator(bpy.types.Operator):
    bl_idname = "create_collection.button"
    bl_label = "Create Collection"
    
    output_list = []
    names_list = []
    

    def execute(self, context):
        scene = context.scene
#        coll_name = scene.coll_name
#        output_path = scene.output_path
        
        output_path = Create_Collection_Operator.output_list[0]
        coll_name = Create_Collection_Operator.names_list[0]

        # Check if collection name and output filepath are entered
        if not coll_name:
            self.report({'ERROR'}, "Please enter a collection name!")
            return {'CANCELLED'}
        if not output_path:
            self.report({'ERROR'}, "Please enter an output filepath!")
            return {'CANCELLED'}

        # Create new collection with given name
        collection = bpy.data.collections.get(coll_name)
        if not collection:
            collection = bpy.data.collections.new(coll_name)
            bpy.context.scene.collection.children.link(collection)
        else:
            self.report({'ERROR'}, f"Collection '{coll_name}' already exists!")
            return {'CANCELLED'}

        # Set collection name for importing models
        bpy.context.scene['collection_for_import'] = coll_name

#        # Add new collection name to enum property in TestPanel_4
#        items = [(c.name, c.name, "") for c in bpy.data.collections]
#        TestPanel_4.collection_enum = bpy.props.EnumProperty(items=items)

        bpy.ops.wm.redraw_timer(type='DRAW_WIN_SWAP', iterations=1)
        
        Create_Collection_Operator.output_list.remove(output_path)
        Create_Collection_Operator.names_list.remove(coll_name)

        return {'FINISHED'}
        
###################################################################

class TestPanel_3(bpy.types.Panel):
    bl_label = "Importar Modelos"
    bl_idname = "VIEW3D_PT_TestPanel_3"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    words = {
        "Enter import path": {"en": "Enter import path:", "pt": "Insira o caminho da importação:"},
        "File format": {"en": "File format:", "pt": "Formato dos arquivos:"},
        "Import Models": {"en": "Import Models", "pt": "Importar Modelos"}
    }

    def draw(self, context):
        layout = self.layout
        scene = context.scene
        
        # Text input for directory path
        layout.label(text=self.words["Enter import path"][bpy.context.window_manager.language])
        layout.prop(scene, "model_path", text="")
        
        # Dropdown menu to choose file format
        layout.label(text=self.words["File format"][bpy.context.window_manager.language])
        layout.prop(scene, "file_format", text="")
        
        # Button for importing templates
        layout.operator("import_models.button", text=self.words["Import Models"][bpy.context.window_manager.language])


class ImportModelsOperator(bpy.types.Operator):
    bl_idname = "import_models.button"
    bl_label = "Import Models"
    
    def execute(self, context):
        scene = context.scene
        model_path = scene.model_path
        file_format = scene.file_format.lower()
        
        # Check if directory exists
        if not os.path.exists(model_path):
            self.report({'ERROR'}, "Directory does not exist!")
            return {'CANCELLED'}
        
        # Get the target collection
        collection = bpy.data.collections.get(context.scene['collection_for_import'])
        if not collection:
            collection = bpy.data.collections.new(coll_name)
            bpy.context.scene.collection.children.link(collection)
        
        # Loop through files in directory and import files based on file format
        for filename in os.listdir(model_path):
            if filename.lower().endswith(f".{file_format}") and os.path.isfile(os.path.join(model_path, filename)):
                filepath = os.path.join(model_path, filename)
                
                # Import file
                if file_format == "stl":
                    bpy.ops.import_mesh.stl(filepath=filepath, directory=model_path)
                elif file_format == "ply":
                    bpy.ops.import_mesh.ply(filepath=filepath, directory=model_path)
                elif file_format == "wrl":
                    bpy.ops.import_scene.x3d(filepath=filepath, directory=model_path)
                
                # Move imported object to collection
                obj = context.selected_objects[0]
                obj_name = obj.name
                if obj_name not in collection.objects:
                    collection.objects.link(obj)
                else:
                    print(f"Object '{obj_name}' already in collection '{coll_name}'")
        
        context.scene.model_path = ""
        
        return {'FINISHED'}

###################################################################

class TestPanel_4(bpy.types.Panel):
    bl_label = "Aparência dos Modelos"
    bl_idname = "VIEW3D_PT_TestPanel_4"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    words = {
        "Select View": {"en": "Select View:", "pt": "Selecione a vista:"},
        "Select Material": {"en": "Select Material:", "pt": "Selecione o material:"},
        "Select Collection": {"en": "Select Collection:", "pt": "Selecione a coleção:"},
        "Apply Changes": {"en": "Apply Changes", "pt": "Aplicar Mudanças"}
    }
    
    bpy.types.Scene.selected_material = bpy.props.StringProperty()

    def draw(self, context):
        layout = self.layout
        scene = context.scene
        
        layout.label(text=self.words["Select View"][bpy.context.window_manager.language])
        layout.prop(scene, "selected_view", text="")
        
        layout.label(text=self.words["Select Material"][bpy.context.window_manager.language])
        layout.prop(scene, "selected_material", text="")
        
        # Dropdown menu for selecting a collection
        row = layout.row()
        row.label(text=self.words["Select Collection"][bpy.context.window_manager.language])
        row = layout.row()
        row.prop_search(scene, "selected_collection", bpy.data, "collections")

        # Button for applying changes to selected collection
        layout.operator("apply_changes.button", text=self.words["Apply Changes"][bpy.context.window_manager.language])
        
        
class Apply_Changes_Operator(bpy.types.Operator):
    bl_idname = "apply_changes.button"
    bl_label = "Apply Changes"

    def execute(self, context):
        scene = context.scene
        selected_collection = scene.selected_collection
        selected_material = scene.selected_material
        selected_view = scene.selected_view

        # Apply changes to selected collection
        collection = bpy.data.collections.get(selected_collection)
        if collection:
            # Set default material properties based on selected material
            if selected_material == "STAINLESS_STEEL":
                diffuse_color = (0.75, 0.75, 0.75, 1)
                metallic = 1.0
                roughness = 0.2
            elif selected_material == "GALVANIZED_STEEL":
                diffuse_color = (0.85, 0.85, 0.85, 1)
                metallic = 0.8
                roughness = 0.4
            elif selected_material == "ZINC_PLATED_STEEL":
                diffuse_color = (0.8, 0.8, 0.8, 1)
                metallic = 0.7
                roughness = 0.3
            elif selected_material == "ALLOY_STEEL":
                diffuse_color = (0.6, 0.6, 0.6, 1)
                metallic = 1.0
                roughness = 0.5
            elif selected_material == "BRASS":
                diffuse_color = (0.9, 0.6, 0.2, 1)
                metallic = 1.0
                roughness = 0.1
            elif selected_material == "BRONZE":
                diffuse_color = (0.85, 0.5, 0.2, 1)
                metallic = 1.0
                roughness = 0.6
                
            for obj in collection.objects:
                if selected_view == "VISO":
                    obj.rotation_euler[0] = 2.0944
                    obj.rotation_euler[1] = 2.0944
                    obj.rotation_euler[2] = 2.0944
                elif selected_view == "VA":
                    obj.rotation_euler[0] = 0
                    obj.rotation_euler[1] = 0
                    obj.rotation_euler[2] = 0
                elif selected_view == "VP":
                    obj.rotation_euler[0] = 0
                    obj.rotation_euler[1] = 0
                    obj.rotation_euler[2] = 3.1416
                elif selected_view == "VS":
                    obj.rotation_euler[0] = -1.5708
                    obj.rotation_euler[1] = 0
                    obj.rotation_euler[2] = 0
                elif selected_view == "VI":
                    obj.rotation_euler[0] = 1.5708
                    obj.rotation_euler[1] = 0
                    obj.rotation_euler[2] = 0
                elif selected_view == "VLE":
                    obj.rotation_euler[0] = 0
                    obj.rotation_euler[1] = -1.5708
                    obj.rotation_euler[2] = 0
                elif selected_view == "VLD":
                    obj.rotation_euler[0] = 0
                    obj.rotation_euler[1] = 1.5708
                    obj.rotation_euler[2] = 0

            # Apply material properties to objects in the collection
            for obj in collection.objects:
                if not obj.data.materials:
                    obj.data.materials.append(bpy.data.materials.new(name=selected_material))
                material = obj.data.materials[0]
                material.diffuse_color = diffuse_color
                material.metallic = metallic
                material.roughness = roughness
                if material.node_tree is not None:
                    material.node_tree.nodes["Principled BSDF"].inputs[0].default_value = diffuse_color
                    material.node_tree.nodes["Principled BSDF"].inputs[6].default_value = metallic
                    material.node_tree.nodes["Principled BSDF"].inputs[9].default_value = roughness

            # Update the viewport to reflect the changes
            for area in context.screen.areas:
                if area.type == 'VIEW_3D':
                    for region in area.regions:
                        if region.type == 'WINDOW':
                            region.tag_redraw()
#                            space_data = area.spaces.active
#                            if space_data.shading.type != 'MATERIAL':
#                                space_data.shading.type = 'MATERIAL'

        else:
            self.report({'ERROR'}, f"Collection '{selected_collection}' not found!")
            return {'CANCELLED'}

        return {'FINISHED'}

###################################################################

class TestPanel_5(bpy.types.Panel):
    bl_label = "Configurar Cena"
    bl_idname = "VIEW3D_PT_TestPanel_5"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    words = {
        "Set Up Scene": {"en": "Set Up Scene", "pt": "Configurar Cena"}
    }
        
    def draw(self, context):
        layout = self.layout
        scene = context.scene
                
        row = layout.row()
        row.operator("render.collection", text=self.words["Set Up Scene"][bpy.context.window_manager.language])
        
        
class RENDER_OT_collection(bpy.types.Operator):
    bl_idname = "render.collection"
    bl_label = "Add Output Path"

    def execute(self, context):
        # Create a scene collection
        collection = bpy.data.collections.new('DEMO')
        collection.name = 'Data Collection'
        bpy.context.scene.collection.children.link(collection)
        data_coll = bpy.data.collections['Data Collection']

        scene_coll = bpy.context.scene.collection.objects

        # Add a light to the scene
        bpy.ops.object.light_add(type='POINT', radius=1, align='WORLD', location=(0, 0, 0), scale=(1, 1, 1))
        data_coll.objects.link(bpy.data.objects['Point'])
        scene_coll.unlink(bpy.data.objects['Point'])

        return {'FINISHED'}

###################################################################

class TestPanel_6(bpy.types.Panel):
    bl_label = "Renderizar Classes"
    bl_idname = "VIEW3D_PT_TestPanel_6"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Panel'
    
    words = {
        "Set Frames Angle": {"en": "Set Frames Angle:", "pt": "Definir Ângulo dos Quadros:"},
        "Set Frames": {"en": "Set Frames", "pt": "Definir Quadros"},
        "Render Collections": {"en": "Render Collections", "pt": "Renderizar Coleção"}
    }
    
    names_list = []
    output_list = []

    def draw(self, context):
        layout = self.layout

        row = layout.row()
        row.label(text=self.words["Set Frames Angle"][bpy.context.window_manager.language])
        row = layout.row()
        row.prop(context.scene, "frames_angle", text="")
        row.operator("render.set_frames_angle", text=self.words["Set Frames"][bpy.context.window_manager.language])
        
        row = layout.row()
        row.operator("render.render_collections", text=self.words["Render Collections"][bpy.context.window_manager.language])
        

class RENDER_OT_set_frames_per_rotation(bpy.types.Operator):
    bl_idname = "render.set_frames_angle"
    bl_label = "Set Frames per Rotation"
    bl_description = "Set the number of frames to be rendered per 360 degrees of the bezier circle path"

    def execute(self, context):
        angle = int(context.scene.frames_angle)
        frame_angles = [str(angle * i) for i in range(1, int(360 / angle) + 1)]
        context.scene.frames_angle = ','.join(frame_angles)
        return {'FINISHED'}


class RENDER_OT_render_collections(bpy.types.Operator):
    bl_idname = "render.render_collections"
    bl_label = "Render Collections"

    def execute(self, context):
        
        # Acessa a configuração do mundo
        world = bpy.context.scene.world

        # Configura a cor do mundo como branco
#        world.use_nodes = False  # Desabilita o uso de nós para simplificar
        world.color = (1, 1, 1)  # Define a cor do mundo como branco
        
        data_coll = bpy.data.collections['Data Collection']
        scene_coll = bpy.context.scene.collection.objects

        frame_angles = context.scene.frames_angle.split(',')

        for obj in bpy.context.scene.objects:
            # Check if the object is visible in render
            if not obj.hide_render:
                # If it's visible, hide it
                obj.hide_render = True

        for i, coll_name in enumerate(TestPanel_6.names_list):
            coll = bpy.data.collections.get(coll_name)
            
            start_time = time.time()
            
            if coll is not None:
                output_path = TestPanel_6.output_list[i % len(TestPanel_6.output_list)]
                for obj in coll.objects:
                    
                    # Adiciona o modificador Laplacian Smooth
                    smooth_modifier = obj.modifiers.new(name='Smooth', type='LAPLACIANSMOOTH')

                    # Define as configurações do modificador
                    smooth_modifier.lambda_factor = 0.0078125  # Define o fator de suavização (quanto maior, mais suave)
                    smooth_modifier.iterations = 1  # Define o número de iterações (quanto maior, mais suave)

                    # Aplica o modificador para torná-lo permanente
                    bpy.ops.object.modifier_apply({"object": obj}, modifier=smooth_modifier.name)
                    
                    obj.select_set(True)  # Seleciona o objeto desejado
                    bpy.context.view_layer.objects.active = obj  # Define o objeto ativo
                    bpy.ops.object.shade_smooth()

                    for other_obj in coll.objects:
                        if other_obj == obj:
                            other_obj.hide_render = False
                        else:
                            other_obj.hide_render = True

                    elemento = 5 * max(obj.dimensions.x, obj.dimensions.y, obj.dimensions.z)

                    bpy.ops.curve.primitive_bezier_circle_add(radius=elemento, enter_editmode=False,
                                                              align='WORLD', location=(0, 0, 0))
                    bezier_circle = bpy.context.object
                    try:
                        data_coll.objects.link(bpy.data.objects['BezierCircle'])
                    except RuntimeError:
                        pass

                    # add a camera to follow the bezier circle path
                    bpy.ops.object.camera_add(enter_editmode=False, align='VIEW', location=(0, 0, 0),
                                              rotation=(1.5708, 0, -1.5708), scale=(1, 1, 1))
                    camera = bpy.context.object

                    # Set up camera to follow bezier circle path
                    follow_path = camera.constraints.new('FOLLOW_PATH')
                    follow_path.target = bezier_circle
                    follow_path.use_curve_follow = True
                    bpy.ops.constraint.followpath_path_animate(constraint="Follow Path", owner='OBJECT')

                    try:
                        data_coll.objects.link(bpy.data.objects['Camera'])
                    except RuntimeError:
                        pass

                    # Set the camera to follow the bezier circle
                    camera = bpy.data.objects.get("Camera")
                    circle = bpy.data.objects.get("BezierCircle")
                    camera.constraints[0].target = circle
                    camera.constraints[0].use_curve_follow = True

                    for frame_angle in frame_angles:
                        # Render the frames
                        bezier_circle.rotation_euler[2] = math.radians(int(frame_angle))
                        bpy.ops.render.render(animation=True)
                        obj_name = obj.name
                        bpy.data.images["Render Result"].save_render(filepath=f"{output_path}/{coll_name}_Frame_{frame_angle}.png")
#                        bpy.ops.render.render(write_still=True)

                    # Reset the camera's constraints
                    camera.constraints[0].target = None
                    camera.constraints[0].use_curve_follow = False

                    # Delete camera and bezier circle from scene
                    bpy.data.objects.remove(camera, do_unlink=True)
                    bpy.data.objects.remove(bezier_circle, do_unlink=True)

                    obj.hide_render = True
                    
            end_time = time.time()
            elapsed_time = end_time - start_time

            print(f"Tempo decorrido: {elapsed_time}s")
                    
        context.scene.frames_angle = ""

        return {'FINISHED'}

###################################################################

def register():
    bpy.utils.register_class(TestPanel_1)
    bpy.utils.register_class(SetLanguageOperator)
    bpy.types.WindowManager.language = bpy.props.StringProperty(default="en")
    bpy.utils.register_class(TestPanel_2)
    bpy.utils.register_class(TestPanel_3)
    bpy.utils.register_class(ImportModelsOperator)
    bpy.types.Scene.model_path = bpy.props.StringProperty(name="Model Path", default="")
    bpy.types.Scene.file_format = bpy.props.EnumProperty(name="File Format", items=[
        ("STL", ".stl", ""),
        ("PLY", ".ply", ""),
        ("WRL", ".wrl", ""),
    ], default="STL")
    bpy.utils.register_class(Create_Collection_Operator)
    bpy.types.Scene.collection_for_import = bpy.props.StringProperty(name="Collection For Import")
    bpy.utils.register_class(TestPanel_4)
    bpy.utils.register_class(Apply_Changes_Operator)
    bpy.types.Scene.selected_collection = bpy.props.StringProperty(name="")
    bpy.types.Scene.selected_material = bpy.props.EnumProperty(
        name="Selected Material",
        items=[("STAINLESS_STEEL", "Stainless Steel", ""),
               ("GALVANIZED_STEEL", "Galvanized Steel", ""),
               ("ZINC_PLATED_STEEL", "Zinc Plated Steel", ""),
               ("ALLOY_STEEL", "Alloy Steel", ""),
               ("BRASS", "Brass", ""),
               ("BRONZE", "Bronze", "")],
        description="Select Material",
        default="STAINLESS_STEEL"
    )
    bpy.utils.register_class(TestPanel_5)
    bpy.types.Scene.selected_view = bpy.props.EnumProperty(
        name="Selected View",
        items=[("VISO", "Isométrica: X=120° | Y=120° | Z=120°", ""),
               ("VA", "Vista frontal anterior: X=0° | Y=0° | Z=0°", ""),
               ("VP", "Vista frontal posterior: X=0° | Y=0° | Z=180°", ""),
               ("VS", "Vista superior: X=-90° | Y=0° | Z=0°", ""),
               ("VI", "Vista inferior: X=90° | Y=0° | Z=0°", ""),
               ("VLE", "Vista lateral esquerda: X=0° | Y=-90° | Z=0°", ""),
               ("VLD", "Vista lateral direita: X=0° | Y=90° | Z=0°", "")],
        description="Select View",
        default="VISO"
    )
    bpy.utils.register_class(TestPanel_6)
    bpy.types.Scene.coll_name = bpy.props.StringProperty(name="Collection Name")
    bpy.types.Scene.output_path = bpy.props.StringProperty(name="Output Path")
    bpy.types.Scene.frames_angle = bpy.props.StringProperty(name="Frames Angle")
    bpy.utils.register_class(RENDER_OT_add_collection_name)
    bpy.utils.register_class(RENDER_OT_add_output_path)
    bpy.utils.register_class(RENDER_OT_render_collections)
    bpy.utils.register_class(RENDER_OT_set_frames_per_rotation)
    bpy.utils.register_class(RENDER_OT_collection)
    
def unregister():
    bpy.utils.unregister_class(TestPanel_1)   
    bpy.utils.unregister_class(SetLanguageOperator)
    del bpy.types.WindowManager.language
    bpy.utils.unregister_class(TestPanel_2)
    bpy.utils.unregister_class(TestPanel_3)
    bpy.utils.unregister_class(ImportModelsOperator)
    del bpy.types.Scene.model_path
    del bpy.types.Scene.file_format
    del bpy.types.Scene.collection_for_import
    bpy.utils.unregister_class(Create_Collection_Operator)
    bpy.utils.unregister_class(TestPanel_4)
    bpy.utils.unregister_class(Apply_Changes_Operator)
    del bpy.types.Scene.selected_collection
    del bpy.types.Scene.selected_material
    bpy.utils.unregister_class(TestPanel_5)
    del bpy.types.Scene.selected_view
    bpy.utils.unregister_class(TestPanel_6)
    bpy.utils.unregister_class(RENDER_OT_add_collection_name)
    bpy.utils.unregister_class(RENDER_OT_add_output_path)
    bpy.utils.unregister_class(RENDER_OT_render_collections)
    bpy.utils.unregister_class(RENDER_OT_set_frames_per_rotation)
    bpy.utils.unregister_class(RENDER_OT_collection)
    del bpy.types.Scene.coll_name
    del bpy.types.Scene.output_path
    del bpy.types.Scene.frames_angle

if __name__ == "__main__":
    register()
