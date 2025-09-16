## ACCIDENT DETECTION WITH A REPORTING SYSTEM

### Overview
Road accidents are a critical public safety issue where early detection and rapid reporting can save lives. This project uses Deep Learning (CNN with VGG16 feature extractor) to detect accidents from video frames and automatically notifies emergency contacts with the detected location and a snapshot via SMS. The system optionally uploads the snapshot to Google Drive and shares a public link in the alert.

### Key Features
- **Accident detection from video**: Extracts frames, preprocesses them, and classifies using a CNN head over VGG16 features.
- **Real-time overlay**: Renders classification (Accident / No Accident) on the video feed.
- **Location lookup**: Uses IP-based geolocation and reverse geocoding to get a human-readable address.
- **Automated SMS alerts**: Sends an SMS via Twilio with the location and a snapshot link.
- **Cloud snapshot sharing**: Uploads the snapshot to Google Drive and generates a public share link.

---

## Project Structure
- `Accident Detection-Video.ipynb`  Main Jupyter Notebook (training + inference + alerting).
- `Accident-1.mp4`  Test video used during inference.
- `Accidents.mp4`  Training video used to extract frames.
- `mapping.csv`  CSV mapping of training image filenames to class labels.
- `test.csv`  CSV mapping of test image filenames to class labels.
- `credentials.json`  Google Service Account credentials for Drive API (not committed).
- Generated at runtime:
  - `0.jpg, 1.jpg, `  Training frames from `Accidents.mp4`
  - `test0.jpg, test1.jpg, `  Test frames from `Accident-1.mp4`
  - `snapshot_YYYYMMDD_HHMMSS.jpg`  Captured alert snapshot

Ensure CSVs reference image paths that exist relative to the notebooks working directory.

---

## Requirements

### Software
- Anaconda Distribution (Python 3.8+ recommended)
- Jupyter Notebook
- Internet access (model weights, geocoding, Twilio, Google APIs)

### Python Packages
- `tensorflow`, `keras`
- `opencv-python`
- `numpy`, `pandas`, `scikit-image`, `scikit-learn`, `matplotlib`
- `geocoder`, `geopy`, `requests`
- `twilio`
- `google-api-python-client`, `google-auth`, `google-auth-oauthlib`

Install with:
```bash
conda create -n accident-detect python=3.10 -y
conda activate accident-detect
pip install tensorflow keras opencv-python numpy pandas scikit-image scikit-learn matplotlib geocoder geopy requests twilio google-api-python-client google-auth google-auth-oauthlib
```

---

## Setup

### 1) Clone and Place Files
- Download/clone the project and keep all files in the same folder.
- Ensure the following exist:
  - `Accident Detection-Video.ipynb`
  - `Accident-1.mp4`
  - `Accidents.mp4`
  - `mapping.csv`
  - `test.csv`

### 2) Twilio (7-day trial available)
- Create a Twilio account.
- Get:
  - `account_sid`
  - `auth_token`
  - `from_` (Twilio phone number)
  - `to` (destination phone number you control)
- In the notebook, replace placeholders:
  - `account_sid = '...'
  - `auth_token = '...'
  - `from_="your_twillio_number"`
  - `to="your_phone_number"`

### 3) Google Drive API (Optional, for snapshot sharing)
- Create a Google Cloud project, enable Google Drive API.
- Create a Service Account and download its JSON key as `credentials.json`.
- Place `credentials.json` next to the notebook.
- Create a target Drive folder and copy its Folder ID, then set:
  - `folder_id = 'your_folder_id'` in the notebook.

### 4) Geolocation
- IP geolocation uses `geocoder.ip('me')`; reverse geocoding uses Nominatim via `geopy`.
- Requires internet access.

---

## How to Run

### A) Launch Notebook
1. Open Anaconda Navigator, launch Jupyter Notebook.
2. Open `Accident Detection-Video.ipynb`.

### B) Configure Notebook
- Set valid values for:
  - Twilio: `account_sid`, `auth_token`, `from_`, `to`
  - Snapshot path: replace `path/to/save/folder` with a real local directory
  - Google Drive: `credentials.json` and `folder_id` (if using uploads)

### C) Execute
- Run all cells in order. The notebook will:
  1. Extract frames from `Accidents.mp4` and `Accident-1.mp4`.
  2. Load `mapping.csv` and `test.csv`, read referenced images.
  3. Resize, preprocess, and extract features with `VGG16(weights='imagenet')`.
  4. Train a small dense classifier head.
  5. Predict on test images.
  6. Render predictions on video frames.
  7. Capture a snapshot near a detected event.
  8. Optionally upload the snapshot to Google Drive and send SMS with location + link.

---

## Configuration Details

- Video sources:
  - Training extraction: `Accidents.mp4`
  - Inference overlay: `Accident-1.mp4`
- CSV formats:
  - `mapping.csv` must include columns like `Image_ID` and `Class`
  - `test.csv` must include `Image_ID`
- Snapshot save path:
  - Replace `os.path.expanduser(r"path/to/save/folder")` with a real folder path (e.g., `r"C:\Users\LENOVO\Pictures\accident_snaps"`).

---

## Notes and Constraints
- VGG16 weights are downloaded automatically on first run.
- `geocoder` and `Nominatim` calls depend on network and API availability; rate limits may apply.
- Twilio trial accounts can only send messages to verified numbers.
- Ensure CSV image paths resolve correctly; missing files will cause I/O errors.
- The demo model is trained on extracted frames and is intended for educational use; results may vary in real-world conditions.

---

## Troubleshooting
- File not found: Verify presence of `Accident-1.mp4`, `Accidents.mp4`, `mapping.csv`, `test.csv`, and CSV-referenced images.
- Google upload fails: Ensure `credentials.json` is valid and has Drive API enabled; verify `folder_id`.
- Twilio errors: Check credentials, verify phone numbers, and trial restrictions.
- No internet: Model weights, geocoding, and APIs require connectivity.
- Video does not display: Ensure a GUI environment for OpenCV windows, or adapt to headless mode.

---

## Future Improvements
- Train end-to-end on a diverse accident dataset.
- Use video-based temporal models (e.g., LSTM/3D CNN/transformers).
- On-device inference and streaming from CCTV/RTSP feeds.
- Integrate reverse-geocoded precise GPS from the capture device.
- Web dashboard for monitoring alerts and reviewing events.

---

## Acknowledgments
- VGG16 via Keras Applications.
- OpenCV for video and rendering.
- Twilio for SMS delivery.
- Google Drive API for snapshot sharing.
- Nominatim and `geocoder` for location services.

---

## License
This project is for educational and demonstration purposes. 

---

## Project By
- Moeen G. Shaikh
