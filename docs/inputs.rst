Input
====================================

Supported Image Formats
------------------------

WormLib supports three microscopy file formats:

**DeltaVision (.dv), Nikon ND2 (.nd2), and TIFF (.tif, .tiff)**
- Multi-channel volumetric images (Z-stacks)
- Native support for 4D data (X, Y, Z, time) and channel extraction



## DeltaVision files follow a specific naming pattern for reference vs. color images:

**Critical Pattern:**

.. code-block:: text

    _R3D_REF  → brightfield reference (2D)
    _R3D      → color/fluorescence (4D multi-channel)


**Reference image (brightfield):**

Contains:
- 2D brightfield transmission image for segmentation
- Single channel per Z-slice

**Color image (fluorescence):**

Contains:
- 4D (C,Z,Y,X) 
- 4 channels (Cy5, mCherry, FITC, DAPI) × Z-slices
- Each channel is a separate index (0, 1, 2, 3)

Do NOT confuse these! The ``_R3D`` file must not include ``_D3D`` in the name and must be a different file from ``_REF``. 

---

File Organization Best Practice
--------------------------------
Give your files informative names at the time of acquisition. Example: ``230713_Lp306_L4440_11_R3D_REF.dv`` = ``date_strain_condition_replicate/``.
Organize your data in a clear folder structure:

data>image_subdirectory>image_files

.. code-block:: text

    data/
    ├── 230713_Lp306_L4440_11/
    │   ├── 230713_Lp306_L4440_11_R3D_REF.dv
    │   └── 230713_Lp306_L4440_11_R3D.dv
    ├── 230713_Lp306_L4440_12/
    │   ├── 230713_Lp306_L4440_12_R3D_REF.dv
    │   └── 230713_Lp306_L4440_12_R3D.dv
    └── 230713_Lp306_L4440_13/
        ├── 230713_Lp306_L4440_13_R3D_REF.dv
        └── 230713_Lp306_L4440_13_R3D.dv

**Benefits:**
- Both reference and color files in same folder allows WormLib to auto-detect and load both
- Easy batch processing 
- Image output subdirectory automatically created in the same folder as input images
---



Loading Images in Code
------------------------

**Automatic file type detection. DeltaVision, Nikon and TIFF files supported :**

.. code-block:: python

    import wormlib
    
    # Path to image subdirectory
    image_path = Path("data/230713_Lp306_L4440_11")
    
    result = wormlib.load_images(
        image_path=str(image_path),
        output_directory="output/",
        channel_names={
            'Cy5': 'set3_mRNA', # Describe what mRNA is in this channel
            'mCherry': 'erm1_mRNA', # Describe what mRNA is in this channel
            'FITC': 'membrane', # Describe what marker is in this channel
            'DAPI': 'DAPI',
            'brightfield': 'brightfield'
        },
        channel_indices={
            'Cy5': 0,
            'mCherry': 1,
            'FITC': 2,
            'DAPI': 3,
            'brightfield': None
        }
    )

WormLib automatically detects ``.dv`` extension and finds both ``_R3D_REF`` and ``_R3D`` files in the same folder. It can load brightfield from the reference file (R3D_REF.dv) and channels from the color file (R3D.dv) and returns organized image data.

**Result structure:**

.. list-table:: Image Data Dictionary
   :widths: 25 20 55
   :header-rows: 1

   * - Key
     - Value/Type
     - Description
   * - image_type
     - str
     - Image format type (e.g., 'DeltaVision')
   * - image_name
     - str
     - Image filename without extension (e.g., '230713_Lp306_L4440_11')
   * - bf
     - numpy array
     - Brightfield (transmission) 2D image (1024, 1024)
   * - image_Cy5
     - numpy array
     - Channel 0 max projection (Cy5 fluorophore)
   * - image_mCherry
     - numpy array
     - Channel 1 max projection (mCherry fluorophore)
   * - image_FITC
     - numpy array
     - Channel 2 max projection (FITC/GFP fluorophore)
   * - image_nuclei
     - numpy array
     - Channel 3 max projection (DAPI/nuclei stain)
   * - Cy5_array
     - numpy array
     - Channel 0 full 3D volume (Z, Y, X)
   * - mCherry_array
     - numpy array
     - Channel 1 full 3D volume (Z, Y, X)
   * - FITC_array
     - numpy array
     - Channel 2 full 3D volume (Z, Y, X)
   * - nuclei_array
     - numpy array
     - Channel 3 full 3D volume (Z, Y, X)
   * - grid_width
     - int
     - Grid width in pixels (80)
   * - grid_height
     - int
     - Grid height in pixels (80)

---
