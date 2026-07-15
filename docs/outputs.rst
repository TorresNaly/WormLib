Output Files and Results Structure
===================================

WormLib generates organized output organized by image name. Each analysis produces visualization PNGs, quantification CSVs, and binary segmentation masks. Outputs are saved flat (no subdirectories) in the image-specific output directory.

---

Output Directory Structure
----------------------------


List of output automatically saved in image subdirectory after single cell spot detection analysis on ``230713_Lp306_L4440_11``:

.. code-block:: text

    output/230713_Lp306_L4440_11/
    ├── channels_230713_Lp306_L4440_11.png
    ├── set3_mRNA_detection_230713_Lp306_L4440_11.png
    ├── set3_mRNA_threshold_230713_Lp306_L4440_11.png
    ├── set3_mRNA_heatmap_230713_Lp306_L4440_11.png
    ├── set3_mRNA_line_scan_230713_Lp306_L4440_11.png
    ├── set3_mRNA_line_ROI_230713_Lp306_L4440_11.png
    ├── set3_mRNA_line_density_data_230713_Lp306_L4440_11.csv
    ├── set3_mRNA_line_scan_data_230713_Lp306_L4440_11.csv
    ├── erm1_mRNA_detection_230713_Lp306_L4440_11.png
    ├── erm1_mRNA_threshold_230713_Lp306_L4440_11.png
    ├── erm1_mRNA_heatmap_230713_Lp306_L4440_11.png
    ├── erm1_mRNA_line_scan_230713_Lp306_L4440_11.png
    ├── erm1_mRNA_line_ROI_230713_Lp306_L4440_11.png
    ├── erm1_mRNA_line_density_data_230713_Lp306_L4440_11.csv
    ├── erm1_mRNA_line_scan_data_230713_Lp306_L4440_11.csv
    ├── masks_cytosol.tif
    ├── per_region_mRNA_counts_230713_Lp306_L4440_11.csv
    ├── total_mRNA_counts_230713_Lp306_L4440_11.csv
    ├── features_df_230713_Lp306_L4440_11.csv
    ├── centroid_position_plot_230713_Lp306_L4440_11.png
    ├── cell_confidence_plot_230713_Lp306_L4440_11.png
    └── predicted_label_230713_Lp306_L4440_11.png


Output Files Reference
-------------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 30 15 40

   * - Example Name
     - Generic Pattern
     - Type
     - Description
   * - ``channels_230713_Lp306_L4440_11.png``
     - ``channels_{image_name}.png``
     - PNG
     - Raw channel images displayed side-by-side for visual inspection
   * - ``set3_mRNA_detection_230713_Lp306_L4440_11.png``
     - ``{channel_name}_detection_{image_name}.png``
     - PNG
     - Detected mRNA spots marked as red/blue points on max projection. One file per RNA channel.
   * - ``set3_mRNA_threshold_230713_Lp306_L4440_11.png``
     - ``{channel_name}_threshold_{image_name}.png``
     - PNG
     - Visualization of threshold applied during spot detection. One file per RNA channel.
   * - ``set3_mRNA_heatmap_230713_Lp306_L4440_11.png``
     - ``{channel_name}_heatmap_{image_name}.png``
     - PNG
     - Side-by-side heatmap figure: (left) max projection with spot density overlay; (right) 80×80 grid showing spot abundance per region. One file per RNA channel.
   * - ``set3_mRNA_line_scan_230713_Lp306_L4440_11.png``
     - ``{channel_name}_line_scan_{image_name}.png``
     - PNG
     - 1D intensity profile along embryo anterior-posterior axis. One file per RNA channel.
   * - ``set3_mRNA_line_ROI_230713_Lp306_L4440_11.png``
     - ``{channel_name}_line_ROI_{image_name}.png``
     - PNG
     - Visual representation of the line scan region of interest on the embryo. One file per RNA channel.
   * - ``masks_cytosol.tif``
     - ``masks_cytosol.tif``
     - TIF
     - Binary segmentation mask of all detected cells/regions (multi-channel format)
   * - ``centroid_position_plot_230713_Lp306_L4440_11.png``
     - ``centroid_position_plot_{image_name}.png``
     - PNG
     - Visualization of cell/region centroids overlaid on embryo image
   * - ``cell_confidence_plot_230713_Lp306_L4440_11.png``
     - ``cell_confidence_plot_{image_name}.png``
     - PNG
     - Plot showing classification confidence scores for each predicted cell identity
   * - ``predicted_label_230713_Lp306_L4440_11.png``
     - ``predicted_label_{image_name}.png``
     - PNG
     - Overlay of predicted cell identity labels on segmented embryo image
   * - ``total_mRNA_counts_230713_Lp306_L4440_11.csv``
     - ``total_mRNA_counts_{image_name}.csv``
     - CSV
     - Wide format summary: total mRNA spot counts per channel for the entire image (useful for bulk statistics)
   * - ``per_region_mRNA_counts_230713_Lp306_L4440_11.csv``
     - ``per_region_mRNA_counts_{image_name}.csv``
     - CSV
     - Long format: spot counts per region, including region_id, channel counts, predicted label, and confidence scores (for statistical analysis and per-cell quantification)
   * - ``features_df_230713_Lp306_L4440_11.csv``
     - ``features_df_{image_name}.csv``
     - CSV
     - Classification features used by Random Forest: centroid position (X, Y), cell area, eccentricity, and other morphological properties
   * - ``set3_mRNA_line_density_data_230713_Lp306_L4440_11.csv``
     - ``{channel_name}_line_density_data_{image_name}.csv``
     - CSV
     - Quantitative spot density values sampled along the line ROI. One file per RNA channel.
   * - ``set3_mRNA_line_scan_data_230713_Lp306_L4440_11.csv``
     - ``{channel_name}_line_scan_data_{image_name}.csv``
     - CSV
     - Raw intensity values sampled along the line scan profile. One file per RNA channel.


CSV Data Format Examples
--------------------------

**total_mRNA_counts_{image_name}.csv** — Quick summary of total counts:

.. code-block:: text

    Image ID,set3_mRNA total molecules,erm1_mRNA total molecules
    230713_Lp306_L4440_11,547,302


**per_region_mRNA_counts_{image_name}.csv** — Per-region analysis data:

.. code-block:: text

    Image ID,region_id,set3_mRNA,erm1_mRNA,label,confidence
    230713_Lp306_L4440_11,1,125,89,AB,0.987
    230713_Lp306_L4440_11,2,98,72,P1,0.954
    230713_Lp306_L4440_11,3,156,104,ABa,0.923
    230713_Lp306_L4440_11,4,168,37,EMS,0.891


**features_df_{image_name}.csv** — Morphological features for classification:

.. code-block:: text

    region_id,centroid_x,centroid_y,area,eccentricity,...
    1,512.5,256.3,18500,0.45,...
    2,520.1,389.2,22100,0.52,...



---

Interpreting Results
---------------------

**Robust spot detection**

- Red/blue dots in spot detection PNG match obvious fluorescent puncta
- Threshold PNG shows appropriate thresholding applied
- Not too noisy (false positives from background)
- Not too conservative (missing real signal)
- Adjust ``spot_radius_nm`` in config if results look off

**Meaningful heatmaps**

- Grid heatmap shows clear spatial patterns (not uniform)
- High-abundance regions correspond to visible spots in image
- Compare between channels to identify differential localization

**Cell classification accuracy**

- Check ``confidence`` column in per_region CSV
- Confidence > 0.9 is generally reliable
- Lower confidence suggests ambiguous cell identity or segmentation artifact
- Use ``cell_confidence_plot`` to visualize prediction reliability across all cells

**Segmentation quality**

- Check ``masks_cytosol.tif`` to verify cell boundaries
- Inspect ``centroid_position_plot`` to confirm region centroids
- Check ``predicted_label`` overlay to see cell identity assignment accuracy

---

Troubleshooting Output Issues
------------------------------

**No output files generated**

- Check pipeline flags in config (e.g., ``spot_detection: true``)
- Verify output_directory exists and is writable
- Check console output for error messages

**Blank or noisy visualizations**

- Verify channel_indices are correct
- Check image data (plot in Jupyter to inspect)
- Confirm PSF values are appropriate for your microscope
- Inspect ``*_threshold_{image_name}.png`` to check if threshold is too aggressive/conservative

**CSV files missing**

- Spot detection must run before quantification CSVs are generated
- Classifier must be enabled for ``label`` and ``confidence`` columns in per_region CSV
- Review pipeline flags in config

**Line scan files missing**

- Ensure ``line_scan_enabled`` is set to ``true`` in config
- Check that line ROI was properly defined for your embryo orientation

---

Next Steps
----------

- See :doc:`models` to understand pre-trained classifiers
- See :doc:`settings` to configure your analysis



