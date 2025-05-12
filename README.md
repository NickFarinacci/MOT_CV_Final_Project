# How to run the code:
- Run the code with the following command to have a square bounding box:
    ```
    python3 final_code.py --input_file <filename> --box_type square --output_file <output_filename> --conf_thresh <0.0-1.0> --iou_thresh <0.0-1.0>
    ```
    - Example:
    ```
    python3 final_code.py --input_file input_files/traffic.mp4 --box_type square --output_file traffic_square --conf_thresh 0.3 --iou_thresh 0.3
    ```
    ```
    python3 final_code_colab.py --input_file input_files/traffic.mp4 --box_type square --output_file traffic_square --conf_thresh 0.3 --iou_thresh 0.3
    ```

- Run the code with the following command to have a segmentation overlay:
    ```
    python3 final_code.py --input_file <filename> --box_type overlay --output_file <output_filename> --conf_thresh <0.0-1.0> --iou_thresh <0.0-1.0>
    ```
    - Example:
    ```
    python3 final_code.py --input_file input_files/traffic.mp4 --box_type overlay --output_file traffic_overlay --conf_thresh 0.3 --iou_thresh 0.3
    ```
    ```
    python3 final_code_colab.py --input_file input_files/traffic.mp4 --box_type overlay --output_file traffic_overlay --conf_thresh 0.3 --iou_thresh 0.3
    ```

# How to run the code in Google Colab:
- Go to the Colab implementation at https://colab.research.google.com/drive/1UgU_yBro7pnM11UKRJfyCd6eiIgn4_sw?usp=sharing
- Run through the script with any input.mp4 file.
- Similar logic just without cv2 functionality.

# Command line arguments:
- input_file: Path to input video file (default: input_files/traffic.mp4)
- box_type: Type of bounding box: square or segmentation overlay (default: square)
- output_file: Custom output filename (without extension) (default: result)
- conf_thresh: Confidence threshold for YOLO detections (0-1) (default: 0.3)
- iou_thresh: IOU threshold for tracking (0-1) (default: 0.3)

# Things to do / Challenges:
- Improve the color detection as it seems a little bit off.
- Improve the speed and size calulcation, we can try to get actual metrics rather than just a range of acceptable values. We can show like mile per hour or width and height in feet, however this will require more extensive computation.
- Try to figure out how to train without using or downloading the full dataset, as this was an issue we ran into.
- Ability to upload and share specific models and datasets through GitHub:
    - We have found that when file sizes are excessive, we can use
    ```
    git config http.postBuffer <buffer_size>
    ```
    to increase the buffer size, which was useful for uploading larger files when we saw the need to do so.
    Example:
    ```
    git config http.postBuffer 524288000
    ```
    This example will increase the buffer size to 500MB, which was useful for uploading the YOLO11x.pt model, datasets, and other input/output files.
    This proved to be a challenge only due to us having to use multiple repositores before we found the solution in ignoring the larger files or increasing the buffer size.

# Evaluation
The system automatically performs comprehensive evaluation after processing each video, including:
- ROC curves per object class
    - ROC curves are a plot of the true positive rate (TPR) against the false positive rate (FPR) at various threshold settings.
- Precision-Recall curves
    - Precision-Recall curves are a plot of the precision (P) against the recall (R) at various threshold settings.
- IoU performance curve
    - IoU performance curve is a plot of the IoU (Intersection over Union) against the threshold settings.
- MOTA (Multiple Object Tracking Accuracy)
    - MOTA is a metric that measures the accuracy of the tracking system.
- IDF1 Score tracking
    - IDF1 Score is a metric that measures the accuracy of the tracking system.
- ID switches analysis
    - ID switches analysis is a metric that measures the number of times the ID of an object changes.
- Track count statistics
    - Track count statistics is a metric that measures the number of objects being tracked.

Results are saved in output_files/<video_name>/evaluation_results/

# Threshold explanations:
- conf_thresh: Confidence threshold (0.0-1.0)
  - Lower values (0.1-0.3): Detect more objects, including distant/unclear ones, but more false positives
  - Default (0.25): Balanced detection
  - Higher values (0.4-0.7): Only detect clear/confident objects, fewer false positives

- iou_thresh: Intersection over Union threshold (0.0-1.0)
  - Lower values (0.1-0.2): Easier to track objects between frames, good for fast/distant objects
  - Default (0.3): Balanced tracking
  - Higher values (0.4-0.6): Stricter tracking, better for clear/close objects

# How to run training (Optional unused/train.py is not fully working):
- The training script is provided for optional custom model training, but pre-trained models are recommended
- Run training with:
    ```
    python3 train.py --data <data.yaml> --base_model <base_model> --save_name <save_name> --epochs <num_epochs> --imgsz <image_size> --batch <batch_size> --device <device> --resume <resume_training>
    ```
- Example:
    ```
    python3 train.py --data coco.yaml --base_model yolo11x.pt --save_name my_model --epochs 100 --imgsz 640 --batch 16 --device 0
    ```

## Training Arguments:
- data: Path to data configuration file (YAML)
- epochs: Number of training epochs (default: 100) 
- batch-size: Batch size for training (default: 16)
- model: Base model to fine-tune (default: yolov11x.pt)
- device: Training device (cuda device, i.e. 0 or 0,1,2,3 or cpu) (default: 0)
- workers: Number of worker threads for data loading (default: 8)
- project: Project name for saving results (default: 'runs/train')
- name: Experiment name (default: 'exp')

## Notes on Training:
- Training requires significant GPU resources and time
- Pre-trained models (yolov11x.pt, yolov11x-seg.pt) are recommended for most use cases
- Custom training should only be considered if you have:
  - A specific dataset different from COCO
  - Need to detect custom object classes
  - Have sufficient computing resources
- Note: This did not work for us during our implementation phase due to time and storage contraints.