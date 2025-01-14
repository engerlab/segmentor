# TiaToolbox 

# Semantic Segmentation 

# How to install gdc-client for data transfer from TCGA data 

- `conda create -n gdc-client-env python=3.8 -y`
- `conda activate gdc-client-env`
- `wget https://gdc.cancer.gov/system/files/public/file/gdc-client_2.3_Ubuntu_x64-py3.8-ubuntu-20.04.zip`
- `unzip /home/yujing/gdc-client_2.3_Ubuntu_x64.zip`
- `gdc-client --version`: now can see gdc-client as an executable 
- `mv gdc-client /home/yujing/miniconda3/envs/gdc-client-env/bin/`
- `gdc-client --version`

# To download TCGA data example 
- `conda activate gdc-client-env`
- manifest file path: `/home/yujing/dockhome/Multimodality/Segment/tmp/manifest/gdc_manifest.2024-11-03.txt`
- cd to where you'd like the 
- `gdc-client download -m/home/yujing/dockhome/Multimodality/Segment/tmp/manifest/gdc_manifest.2024-11-03.txt`
- download the TCGA-CESC data
    - cd /Data/Yujing/Segment/tmp/tcga_cesc_svs
    - gdc-client download -m/Data/Yujing/Segment/tmp/tcga_cesc_manifest/gdc_manifest.2024-11-11.txt
    - note that `/Data/Yujing/Segment/tmp/tcga_cesc_manifest/gdc_manifest.2024-11-11_v2.txt` is the same as `.//Data/Yujing/Segment/tmp/tcga_cesc_manifest/gdc_manifest.2024-11-11.txt` while excluding the cases already downloaded on `/Data/Yujing/Segment/tmp/tcga_cesc_manifest/run_partition/run_Cedar_HG_filemap.tsv`, just to continue downloading 
    - gdc-client download -m//Data/Yujing/Segment/tmp/tcga_cesc_manifest/gdc_manifest.2024-11-11_v2.txt

# Semantic segmetation 
- on test slides `./dockhome/Multimodality/Segment/tmp/blca_svs/30e4624b-6f48-429b-b1d9-6a6bc5c82c5e/TCGA-2F-A9KO-01Z-00-DX1.195576CF-B739-4BD9-B15B-4A70AE287D3E.svs`
- scripts: 
    - dockhome/Multimodality/Segment/tmp/scripts/semantic_segmentation.py
    - dockhome/Multimodality/Segment/tmp/scripts/visualize_semantic_segmentation.py
        - ./visualize_semantic_segmentation.py visualizes the segmentation outpus 
        - WSI outputs 5 channels of probability maps for each class: label_dict = {"Tumour": 0, "Stroma": 1, "Inflammatory": 2, "Necrosis": 3, "Others": 4}

1. Must map pixel by pixel of semantic segmentation output to the Pan-Cancer-Nuclei-Seg .csv 4k by 4k files 
2. Merge semantic segmentation output to the Pan-Cancer-Nuclei-Seg .csv 4k by 4k files
- Once a pixel class is obtained, within a QAed segmented nuclei (number of pixels), majority vote for classification of nuclei class
    - PixelInAreas was already reported in the Pan-Cancer-Nuclei-Seg .csv files
    - Output for each patch, PixelInAreas vector for each class
    - Output for each WSI: concatenation of PixelInAreas vectors for each class ffrom each patch 

# nuclei_classify.py can classify invididual nuclei (segmentation) to semantic classes based on its overlap 
- Per patch analysis available. ~16 seconds per patch. 
- Now Testing for all patches in a folder. Optimize speed
    - Test folder path:
- Need to match file names

## Is it possible to directly mount, read, and write files from ssh Narval to this local machine?
`sshfs your_username@narval.compute_canada.ca:/remote/path/to/folder /local/mount/point`
sshfs yujingz@narval.compute_canada.ca:/home/yujingz/scratch/NUCLEI_SIZE_CODE/Pan-Cancer-Nuclei-Seg/ScientificData/svs-files/tcga_cesc /Data/Yujing/Segment/tmp/tcga_cesc

## Partitioning the semantic segmentation task on multiple compute clusters 
- https://docs.google.com/spreadsheets/d/1RuG-e45JQRM5R15ijCUWbn3fyhfOJsIG9Im8_PFep5Q/edit?usp=sharing
    - shows this partitioning of the task
    - Proton GPU (local), Narval_YZ, Narval_HG, Cedar_YZ, Cedar_HG, Beluga_YZ, Beluga_HG
    - Estimate how long each one takes 
