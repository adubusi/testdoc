.. code:: ipython3

    #Get all wall types
    import ifcopenshell#Importing the ifcopenshell module
    ifc_file = ifcopenshell.open('ifc_file.ifc')#open the ifc model,the 'model.ifc' it the file's name
    for wall_type in ifc_file.by_type("IfcWallType"):
        print("The wall type element is", wall_type)
        print("The name of the wall type is", wall_type.Name)

.. code:: ipython3

    #Get all door occurrences of a type
    import ifcopenshell
    import ifcopenshell.util.element
    model = ifcopenshell.open("model.ifc")
    for door_type in model.by_type("IfcDoorType"):
        print("The door type is", door_type.Name)
        doors = ifcopenshell.util.element.get_types(door_type)
        print(f"There are {len(doors)} of this type")
        for door in doors:
            print("The door name is", door.Name)

.. code:: ipython3

    #Get the properties of a wall type
    import ifcopenshell.util.element
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    wall_type = ifcopenshell.util.element.get_type(wall)
    print(wall_type)

.. code:: ipython3

    # Get all properties and quantities as a dictionary
    # returns {"Pset_WallCommon": {"id": 123, "FireRating": "2HR", ...}}
    import ifcopenshell.util.element
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    wall_type = ifcopenshell.util.element.get_type(wall)
    psets = ifcopenshell.util.element.get_psets(wall_type)
    print(psets)

.. code:: ipython3

    # Get all properties and quantities of the wall, including inherited type properties
    import ifcopenshell.util.element
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    psets = ifcopenshell.util.element.get_psets(wall)
    print(psets)

.. code:: ipython3

    # Get only properties and not quantities
    import ifcopenshell.util.element
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    print(ifcopenshell.util.element.get_psets(wall, psets_only=True))

.. code:: ipython3

    # Get only quantities and not properties
    import ifcopenshell.util.element
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    print(ifcopenshell.util.element.get_psets(wall, qtos_only=True))

.. code:: ipython3

    #Get all elements in a container
    import ifcopenshell.util.element
    for storey in model.by_type("IfcBuildingStorey"):
        elements = ifcopenshell.util.element.get_decomposition(storey)
        print(f"There are {len(elements)} located on storey {storey.Name}, they are:")
        for element in elements:
            print(element.Name)

.. code:: ipython3

    #Get the XYZ coordinates of a element
    import ifcopenshell.util.placement
    wall = model.by_type("IfcWall")[0]# Attributes are ordered and may also be accessed via index.
    # This returns a 4x4 matrix, including the location and rotation. For example:
    # array([[ 1.00000000e+00,  0.00000000e+00,  0.00000000e+00, 2.00000000e+00],
    #        [ 0.00000000e+00,  1.00000000e+00,  0.00000000e+00, 3.00000000e+00],
    #        [ 0.00000000e+00,  0.00000000e+00,  1.00000000e+00, 5.00000000e+00],
    #        [ 0.00000000e+00,  0.00000000e+00,  0.00000000e+00, 1.00000000e+00]])
    matrix = ifcopenshell.util.placement.get_local_placement(wall.ObjectPlacement)
    # The last column holds the XYZ values, such as:
    # array([ 2.00000000e+00,  3.00000000e+00,  5.00000000e+00])
    print(matrix[:,3][:3])

.. code:: ipython3

    #Convert to and from SI units and project units
    import ifcopenshell
    import ifcopenshell.util.unit
    model = ifcopenshell.open("model.ifc")
    # Note: ifc_project_length is a value you have extracted from the project,
    # just as from a quantity set.
    unit_scale = ifcopenshell.util.unit.calculate_unit_scale(model)
    # Convert to SI unit:
    si_meters = ifc_project_length * unit_scale
    # Convert from SI unit:
    ifc_project_length = si_meters / unit_scale

.. code:: ipython3

    # get the area of a space
    import ifcopenshell
    model = ifcopenshell.open("model.ifc")
    space =  model.by_type('IfcSpace')[2]# Attributes are ordered and may also be accessed via index.
    import ifcopenshell.util.placement
    space_area=ifcopenshell.util.placement.get_local_placement(space.ObjectPlacement)[2,3]#get the area of a space
    print(space_area)

.. code:: ipython3

    # select elements from ifc model
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    elements = ifcopenshell.util.selector.filter_elements(model, "IfcWall")# Select all walls in the file.
    print(elements)

.. code:: ipython3

    # select elements from ifc model
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    elements = ifcopenshell.util.selector.filter_elements(model, "IfcWall")# Select all walls in the file.
    elements = ifcopenshell.util.selector.filter_elements(model, "IfcDoor", elements)# Add doors to the elements too.
    print(elements)

.. code:: ipython3

    # get the BuildingStorey Value on each of spaces.
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    spaces=model.by_type('IfcSpace')
    for space in spaces:
            print('Room',space.Name,' is at floor:',space.Decomposes[0][4][2])# get the BuildingStorey Value of spaces.

.. code:: ipython3

    # Get a description of the space function
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    space=model.by_type('IfcSpace')[0]# Attributes are ordered and may also be accessed via index.
    print(ifcopenshell.util.selector.get_element_value(space, "type.Name"))#the type.name of the space is the space function name

.. code:: ipython3

    # Get GlobalId of a storey
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    building_storey = model.by_type('IfcBuildingStorey')[0]# Attributes are ordered and may also be accessed via index.
    print(building_storey.GlobalId)# Get GlobalId of a storey

.. code:: ipython3

    # Get GlobalId of a element, like wall
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    wall = model.by_type('IfcWall')[0]# Attributes are ordered and may also be accessed via index.
    print(wall.GlobalId)# Get GlobalId of a storey

.. code:: ipython3

    #Gets element shape aspects
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    element = model.by_type("IfcWall")[0]
    shape_aspect = ifcopenshell.util.element.get_shape_aspects(element)
    print(shape_aspect)

.. code:: ipython3

    #Gets the first IfcBuildingStorey spatial element that an element is contained in.
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    wall = model.by_type("IfcWall")[0]
    wall_storey=ifcopenshell.util.selector.get_element_value(wall, "storey")
    print(wall_storey)

.. code:: ipython3

    #Get the Name attribute of the element’s relating type.
    import ifcopenshell
    import ifcopenshell.util.selector
    model = ifcopenshell.open("model.ifc")
    wall = model.by_type("IfcWall")[0]
    wall_type_name=ifcopenshell.util.selector.get_element_value(wall, "type.Name")# Get the Name attribute of the wall's type.
    print(wall_type_name)
