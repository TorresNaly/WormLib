Input
====================================

Supported Image Formats
------------------------

File formats supported by WormLib:

- **DeltaVision** (``.dv``)
- **Nikon ND2** (``.nd2``)
- **TIFF** (``.tif``, ``.tiff``)



DeltaVision files
------------------------

Use one folder per acquisition. A typical DeltaVision folder looks like:

.. code-block:: text

    my_experiment/
    └── input/
        └── embryo_001/
            ├── embryo_001_R3D.dv       # 4D stack: channel, z, y, x
            └── embryo_001_R3D_REF.dv   # 2D brightfield/reference image


WormLib assigns channels by zero-based order. These defaults match the bundled example:

.. list-table:: DeltaVision Channel Assignment
   :widths: 20 20 30
   :header-rows: 1

   * - Channel Index
     - WormLib Name
     - Typical Use
   * - 0
     - ``Cy5``
     - RNA channel 1
   * - 1
     - ``mCherry``
     - RNA channel 2
   * - 2
     - ``FITC``
     - Optional channel
   * - 3
     - ``DAPI``
     - Nuclei

If your channel order is different, set the `index` field for each channel
in your input configuration.


**File Naming Convention:**

.. code-block:: text

    embryo_001_R3D_REF.dv    # Reference image (brightfield)
    embryo_001_R3D.dv        # Color image (fluorescence)

**Reference image** (``_R3D_REF``)
  - Brightfield reference (2D)
  - Single channel 2D brightfield
  - Used for embryo segmentation and cell identity prediction

**Color image** (``_R3D``)
  - Fluorescence channels (4D multi-channel)
  - 4D (C, Z, Y, X): C = channel, Z = z-slice, Y = height, X = width
  - Used for smFISH spot detection and spatial mRNA analysis

.. important::

   Do NOT confuse these files! The ``_R3D`` file must NOT include ``_D3D`` in the name and must be a different file from ``_R3D_REF``.

WormLib automatically detects the ``.dv`` extension and finds both ``_R3D_REF`` and ``_R3D`` files in the same folder, loading brightfield from the reference file and channels from the color file.

---

Nikon ND2 Files
------------------------

**File Naming Convention:**

.. code-block:: text

    image_01.nd2




For ``.nd2`` files, channels are assigned in this zero-based order by default:

.. list-table:: Nikon ND2 Channel Assignment
   :widths: 20 20 30
   :header-rows: 1

   * - Channel Index
     - WormLib Name
     - Typical Use
   * - 0
     - ``Cy5``
     - RNA channel 1
   * - 1
     - ``mCherry``
     - RNA channel 2
   * - 2
     - ``FITC``
     - Optional channel
   * - 3
     - ``DAPI``
     - Nuclei
   * - 4
     - ``brightfield``
     - Brightfield/reference

If a channel is missing, set its `name` to `None` in the input configuration in the environment-variable interface.

---

TIFF Files
------------------------

**File Naming Convention:**

.. code-block:: text

    image_01.tif


File Organization Best Practice
--------------------------------

Give your files informative names at the time of acquisition.

**Example naming scheme:** ``230713_Lp306_L4440_11_R3D_REF.dv``
  - ``230713`` = date
  - ``Lp306`` = strain
  - ``L4440`` = condition
  - ``11`` = replicate

Organize your data in a clear folder structure: ``data/ → image_subdirectory/ → image_files``

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

- Both reference and color files in the same folder allow WormLib to auto-detect and load both
- Enables easy batch processing
- Image output subdirectory automatically created in the same folder as input images



Loading Images in Code
------------------------

WormLib automatically detects file types. DeltaVision, Nikon, and TIFF files are supported.

.. code-block:: python

    import wormlib

    # Path to image subdirectory
    image_path = Path("data/230713_Lp306_L4440_11")
    output_directory = Path("output/")
    
    result = wormlib.load_images(
        image_path=str(image_path),
        output_directory=str(output_directory),
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


